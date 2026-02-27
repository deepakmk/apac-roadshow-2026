# Pattern Composition Guide

## From Learning to Production

This guide shows how the **4 isolated pattern modules** come together in a production Flink job.

---

## 🎓 Learning Path

### Phase 1: Learn Patterns Individually

Study each pattern in isolation:

```
patterns/
├── 01_hybrid_source/     ← Learn bounded→unbounded
├── 02_keyed_state/       ← Learn per-key state
├── 03_timers/            ← Learn timeout detection
└── 04_side_outputs/      ← Learn multi-way routing
```

**Time:** ~4 hours (45-60 min each)

### Phase 2: Understand Shared Components

Review reusable components:

```
shared/
├── model/                ← InventoryEvent, AlertEvent, EventType
├── config/               ← InventoryConfig, KafkaTopics, StateConfig
└── processor/            ← ProductParser, InventoryStateFunction, SinkFactory
```

These combine patterns into reusable classes.

### Phase 3: See Pattern Composition

Run the complete job:

```java
InventoryManagementJobRefactored.java
```

This shows how all patterns work together.

---

## 🏗️ Architecture: Pattern Composition

### Data Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                      INVENTORY MANAGEMENT JOB                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  STEP 1: Configuration                                          │
│  ┌──────────────────────────────────────────────────────┐       │
│  │ InventoryConfig.fromEnvironment()                    │       │
│  │ - Kafka bootstrap servers                            │       │
│  │ - Topic names                                        │       │
│  │ - Checkpoint interval                                │       │
│  │ - Parallelism                                        │       │
│  └──────────────────────────────────────────────────────┘       │
│                          │                                      │
│                          ▼                                      │
│  STEP 2: Pattern 01 - Hybrid Source                             │
│  ┌──────────────────────────────────────────────────────┐       │
│  │ HybridSource<String>                                 │       │
│  │ ┌─────────────┐         ┌─────────────┐              │       │
│  │ │ File Source │────────▶│   Hybrid    │              │       │
│  │ │ (Bounded)   │         │   Source    │              │       │
│  │ └─────────────┘         │             │              │       │
│  │ ┌─────────────┐         └──────┬──────┘              │       │
│  │ │Kafka Source │────────────────┘                     │       │
│  │ │(Unbounded)  │                                      │       │
│  │ └─────────────┘                                      │       │
│  └──────────────────────────────────────────────────────┘       │
│                          │                                      │
│                          ▼ (String: JSON)                       │
│  STEP 3: Parse JSON                                             │
│  ┌──────────────────────────────────────────────────────┐       │
│  │ ProductParser.processElement()                       │       │
│  │ - Handle JSON arrays and single objects              │       │
│  │ - Error handling (log, don't fail)                   │       │
│  │ - Validation                                         │       │
│  └──────────────────────────────────────────────────────┘       │
│                          │                                      │
│                          ▼ (Product objects)                    │
│  STEP 4: Key by Product ID                                      │
│  ┌──────────────────────────────────────────────────────┐       │
│  │ .keyBy(product -> product.productId)                 │       │
│  │                                                      │       │
│  │ Partitions data by product ID across parallel tasks  │       │
│  └──────────────────────────────────────────────────────┘       │
│                          │                                      │
│                          ▼                                      │
│  STEP 5: Patterns 02, 03, 04 Combined                           │
│  ┌──────────────────────────────────────────────────────┐       │
│  │ InventoryStateFunction                               │       │
│  │ extends KeyedProcessFunction<String, Product,        │       │
│  │                              InventoryEvent>         │       │
│  │                                                      │       │
│  │ ┌────────────────────────────────────────────────┐   │       │
│  │ │ Pattern 02: Keyed State                        │   │       │
│  │ │ - lastProductState: ValueState<Product>        │   │       │
│  │ │ - lastUpdateTimeState: ValueState<Long>        │   │       │
│  │ │ - timerState: ValueState<Long>                 │   │       │
│  │ │                                                │   │       │
│  │ │ Track inventory changes per product:           │   │       │
│  │ │ - New products                                 │   │       │
│  │ │ - Inventory increases/decreases                │   │       │
│  │ │ - Price changes                                │   │       │
│  │ └────────────────────────────────────────────────┘   │       │
│  │                                                      │       │
│  │ ┌────────────────────────────────────────────────┐   │       │
│  │ │ Pattern 03: Timers                             │   │       │
│  │ │ - Register 1-hour timer on each update         │   │       │
│  │ │ - Delete old timer before new one              │   │       │
│  │ │ - onTimer(): Emit stale inventory alert        │   │       │
│  │ └────────────────────────────────────────────────┘   │       │
│  │                                                      │       │
│  │ ┌────────────────────────────────────────────────┐   │       │
│  │ │ Pattern 04: Side Outputs                       │   │       │
│  │ │ - LOW_STOCK_TAG (inventory < 10)               │   │       │
│  │ │ - OUT_OF_STOCK_TAG (inventory = 0)             │   │       │
│  │ │ - PRICE_DROP_TAG (price down >10%)             │   │       │
│  │ │                                                │   │       │
│  │ │ ctx.output(tag, alert)                         │   │       │
│  │ └────────────────────────────────────────────────┘   │       │
│  └──────────────────────────────────────────────────────┘       │
│                          │                                      │
│         ┌────────────────┼────────────────┬────────────┐        │
│         ▼                ▼                ▼            ▼        │
│    Main Output    Low Stock      Out of Stock    Price Drop     │
│  InventoryEvent    AlertEvent      AlertEvent     AlertEvent    │
│         │                │                │            │        │
│         │                └────────────────┴────────────┘        │
│         │                          │                            │
│         │                          ▼                            │
│         │                    Union All Alerts                   │
│         │                          │                            │
│  STEP 6: Sinks                     │                            │
│  ┌──────▼───────────────────┐  ┌───▼───────────────────────┐    │
│  │ SinkFactory              │  │ SinkFactory               │    │
│  │ .sinkInventoryEvents()   │  │ .sinkAlerts()             │    │
│  │                          │  │                           │    │
│  │ Topic: inventory-events  │  │ Topic: inventory-alerts   │    │
│  └──────┬───────────────────┘  └───────────────────────────┘    │
│         │                                                       │
│         └──────────────────┐                                    │
│                     ┌──────▼───────────────────┐                │
│                     │ SinkFactory              │                │
│                     │ .sinkToWebSocket()       │                │
│                     │                          │                │
│                     │ Topic: websocket_fanout  │                │
│                     │ (Real-time UI updates)   │                │
│                     └──────────────────────────┘                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 💻 Code Walkthrough

### Original Job (476 lines) ❌

**Problems:**
- Everything in one file
- Hard to learn individual patterns
- Difficult to test components
- Mixed concerns (business logic + I/O + state)

### Refactored Job (150 lines) ✅

**Benefits:**
- Clear pattern composition
- Reusable components
- Easy to understand flow
- Educational structure

```java
public static void main(String[] args) throws Exception {
    // STEP 1: Setup
    StreamExecutionEnvironment env = ...;
    InventoryConfig config = InventoryConfig.fromEnvironment();
    env.setParallelism(config.getParallelism());
    env.enableCheckpointing(config.getCheckpointInterval());

    // STEP 2: Pattern 01 - Hybrid Source
    HybridSource<String> source = HybridSourceExample.createHybridSource(
        config.getInitialProductsFile(),
        config.getKafkaBootstrapServers()
    );
    DataStream<String> rawStream = env.fromSource(source, ...);

    // STEP 3: Parse
    DataStream<Product> products = rawStream
        .process(new ProductParser());

    // STEP 4 & 5: Patterns 02, 03, 04 - Keyed State + Timers + Side Outputs
    SingleOutputStreamOperator<InventoryEvent> events = products
        .keyBy(p -> p.productId)
        .process(new InventoryStateFunction());

    // STEP 6: Get side outputs
    DataStream<AlertEvent> lowStock = events.getSideOutput(LOW_STOCK_TAG);
    DataStream<AlertEvent> outOfStock = events.getSideOutput(OUT_OF_STOCK_TAG);
    DataStream<AlertEvent> priceDrop = events.getSideOutput(PRICE_DROP_TAG);
    DataStream<AlertEvent> allAlerts = lowStock.union(outOfStock, priceDrop);

    // STEP 7: Sinks
    SinkFactory.sinkInventoryEvents(events, config);
    SinkFactory.sinkAlerts(allAlerts, config);
    SinkFactory.sinkToWebSocket(events, config);

    env.execute("Inventory Management Job");
}
```

**Line count comparison:**
- Main method: 476 lines → 150 lines (68% reduction!)
- Total with shared components: 476 lines → ~800 lines (but reusable!)

---

## 🧩 Pattern Interactions

### How Patterns Work Together

#### Pattern 01 + Pattern 02
**Hybrid Source → Keyed State**

```
File: initial-products.json (1000 products)
  ↓
Bootstrap state with all 1000 products
  ↓
Switch to Kafka for updates
  ↓
State already initialized ✓
Updates apply immediately
```

**Why it works:**
- File loads all products first
- State is populated before Kafka starts
- No "cold start" - state is warm

#### Pattern 02 + Pattern 03
**Keyed State → Timers**

```
Product update arrives
  ↓
Update lastProductState (Pattern 02)
  ↓
Delete old timer (Pattern 03)
  ↓
Register new timer +1 hour (Pattern 03)
  ↓
Save timer timestamp in timerState (Pattern 02)
```

**Why it works:**
- State persists timer timestamp
- Can delete old timer on next update
- Timer survives checkpoints

#### Pattern 03 + Pattern 04
**Timers → Side Outputs**

```
Timer fires (no update for 1 hour)
  ↓
onTimer() called
  ↓
Create stale inventory event
  ↓
Emit to main output (could use side output too!)
```

**Alternative: Emit stale to side output**

#### Pattern 02 + Pattern 04
**Keyed State → Side Outputs**

```
processElement() called
  ↓
Check state: product.inventory < 10?
  ↓
YES: ctx.output(LOW_STOCK_TAG, alert)
  ↓
Continue processing
  ↓
Emit to main output too
```

**Why it works:**
- One input → multiple outputs
- No reprocessing needed
- Type-safe routing

---

## 🎯 Teaching Moments

### For Workshop Participants

**After learning patterns individually:**

1. **"How do I combine patterns?"**
   → See `InventoryStateFunction` - combines 02, 03, 04

2. **"When do I use which pattern?"**
   → See the decision guide below

3. **"How do I structure my job?"**
   → See `InventoryManagementJobRefactored` main method

4. **"Can I reuse these components?"**
   → Yes! `shared/` folder has reusable classes

### Decision Guide

| Requirement | Pattern | Why |
|-------------|---------|-----|
| Bootstrap from historical data | 01: Hybrid Source | Load state before streaming |
| Track per-entity metrics | 02: Keyed State | Automatic partitioning |
| Detect timeouts/stale data | 03: Timers | Time-based events |
| Route by type/severity | 04: Side Outputs | Avoid multiple filters |
| All of the above | Composition | Combine patterns! |

---

## 🚀 Running the Composed Job

### Prerequisites

```bash
# 1. Start Kafka
docker compose up -d redpanda-0 redpanda-1 redpanda-2

# 2. Create sample data
mkdir -p data
cat > data/initial-products.json << 'EOF'
[
  {"productId":"LAPTOP_001","name":"Gaming Laptop","inventory":10,"price":1299.99,"category":"Electronics"},
  {"productId":"MOUSE_001","name":"Wireless Mouse","inventory":50,"price":29.99,"category":"Electronics"},
  {"productId":"KEYBOARD_001","name":"Mechanical Keyboard","inventory":30,"price":89.99,"category":"Electronics"}
]
EOF
```

### Build and Run

```bash
# Build Flink jobs with Java 11
./build-flink.sh

# Run the refactored job
KAFKA_BOOTSTRAP_SERVERS=localhost:19092 \
INITIAL_PRODUCTS_FILE=data/initial-products.json \
java -cp flink-inventory/build/libs/flink-inventory.jar \
  com.ververica.composable_job.flink.inventory.InventoryManagementJobRefactored
```

### Test the Job

```bash
# Publish updates to Kafka
docker exec -it redpanda-0 rpk topic produce product-updates

# Type (Ctrl+D to send, Ctrl+C to exit):
{"productId":"LAPTOP_001","name":"Gaming Laptop","inventory":8,"price":1299.99,"category":"Electronics"}
{"productId":"MOUSE_001","name":"Wireless Mouse","inventory":5,"price":29.99,"category":"Electronics"}
{"productId":"LAPTOP_001","name":"Gaming Laptop","inventory":8,"price":999.99,"category":"Electronics"}
```

### Expected Output

```
🚀 Starting Inventory Management Job
📊 Parallelism: 2
💾 Checkpoint interval: 60000ms

📥 PATTERN 01: Creating Hybrid Source (File → Kafka)
✓ File source created
✓ Kafka source created
✓ Hybrid source ready

🔄 Parsing JSON to Product objects
🆕 New product: LAPTOP_001 with inventory: 10
🆕 New product: MOUSE_001 with inventory: 50
🆕 New product: KEYBOARD_001 with inventory: 30

📊 Inventory change for LAPTOP_001: 10 → 8 (INVENTORY_DECREASED)
⚠️  LOW STOCK alert for MOUSE_001: 5 items
💰 Price change for LAPTOP_001: $1299.99 → $999.99 (PRICE_CHANGED)
💸 PRICE DROP alert for LAPTOP_001: 23.1% off
```

---

## 📚 Learning Exercises

### Exercise 1: Modify Pattern Behavior

**Task:** Change the stale timeout from 1 hour to 30 minutes.

**Files to modify:**
- `InventoryStateFunction.java` line: `STALE_TIMEOUT_MS = 60 * 60 * 1000`
- Change to: `STALE_TIMEOUT_MS = 30 * 60 * 1000`

**Test:** Product with no updates for 30 min should trigger alert.

### Exercise 2: Add New Side Output

**Task:** Add a HIGH_VALUE side output for products over $1000.

**Steps:**
1. Add `HIGH_VALUE_TAG` in `InventoryStateFunction`
2. Check price in `processElement()`
3. Emit to side output if `price > 1000`
4. Get side output in main job
5. Add sink for high-value alerts

### Exercise 3: Add Metrics

**Task:** Track total inventory value per category.

**Hint:** Use `ReducingState` with custom `ReduceFunction`.

**Pattern:** Extend Pattern 02 (Keyed State) knowledge.

---

## 🎓 Next Level

Once comfortable with pattern composition, explore:

### Advanced Patterns

**Recommendations Module:**
- Session Windows (shopping sessions)
- Broadcast State (ML model distribution)
- CEP (cart abandonment detection)

**Integration:**
- Apache Paimon (unified batch-stream storage)
- Exactly-once semantics end-to-end
- State migration and schema evolution

---

## ✅ Checklist

After completing this guide, you should be able to:

- [ ] Explain each pattern's role in the pipeline
- [ ] Identify pattern interactions (how they work together)
- [ ] Modify individual patterns without breaking others
- [ ] Add new patterns to the composition
- [ ] Structure production Flink jobs with pattern composition
- [ ] Debug issues by pattern (isolate which pattern has the bug)

---

## 📖 Further Reading

- Pattern 01: [Hybrid Source README](patterns/01_hybrid_source/README.md)
- Pattern 02: [Keyed State README](patterns/02_keyed_state/README.md)
- Pattern 03: [Timers README](patterns/03_timers/README.md)
- Pattern 04: [Side Outputs README](patterns/04_side_outputs/README.md)
- [Flink DataStream API](https://nightlies.apache.org/flink/flink-docs-release-1.20/)

---

<div align="center">
  <b>From Learning to Production</b><br>
  <i>Master patterns individually, then compose them into production systems</i>
</div>
