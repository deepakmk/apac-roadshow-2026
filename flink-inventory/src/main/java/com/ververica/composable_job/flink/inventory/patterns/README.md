# 📦 Flink Inventory Patterns

> Learn foundational Apache Flink patterns through practical inventory management examples

---

## 📚 Pattern Learning Modules

This directory contains **4 foundational Flink patterns** designed for teaching and hands-on learning. Each pattern includes:

✅ **Standalone runnable example** (`*Example.java`)
✅ **Comprehensive README** (600-1,400 lines with exercises, quizzes, solutions)
✅ **Real-world use cases** (inventory management, IoT, transaction monitoring)
✅ **Performance tips** and common pitfalls

**Flink Version:** 1.20.0
**Java Version:** 11 (required for Flink 1.20)
**Kafka Version:** 3.8.1

---

## 🗂️ Pattern Catalog

### Pattern 01: Hybrid Source 🔄

**File:** [`01_hybrid_source/HybridSourceExample.java`](01_hybrid_source/HybridSourceExample.java)
**README:** [`01_hybrid_source/README.md`](01_hybrid_source/README.md)

**What you'll learn:**
- Bootstrap state from files before streaming from Kafka
- Switch seamlessly from bounded to unbounded sources
- Initialize inventory from historical data
- Handle source switching without data loss

**Use cases:**
- Cold start with historical data
- State initialization from backups
- Migration from batch to streaming
- Disaster recovery scenarios

**Difficulty:** Beginner | **Estimated time:** 45 minutes

---

### Pattern 02: Keyed State 🔑

**File:** [`02_keyed_state/KeyedStateExample.java`](02_keyed_state/KeyedStateExample.java)
**README:** [`02_keyed_state/README.md`](02_keyed_state/README.md)

**What you'll learn:**
- Manage per-key state with fault tolerance
- Use ValueState, ListState, MapState
- Track inventory changes per product
- Understand state partitioning and checkpointing

**Use cases:**
- Per-product inventory tracking
- User session management
- Account balance calculations
- Device state monitoring

**Difficulty:** Beginner | **Estimated time:** 60 minutes

---

### Pattern 03: Timers ⏰

**File:** [`03_timers/TimerExample.java`](03_timers/TimerExample.java)
**README:** [`03_timers/README.md`](03_timers/README.md)

**What you'll learn:**
- Implement processing time and event time timers
- Detect stale inventory with timeout logic
- Register and manage timer callbacks
- Handle timer expiration and cleanup

**Use cases:**
- Timeout detection (stale inventory, idle sessions)
- Scheduled processing (hourly reports)
- SLA monitoring (response time alerts)
- Reminder systems (abandoned cart emails)

**Difficulty:** Intermediate | **Estimated time:** 60 minutes

---

### Pattern 04: Side Outputs 🎯

**File:** [`04_side_outputs/SideOutputExample.java`](04_side_outputs/SideOutputExample.java)
**README:** [`04_side_outputs/README.md`](04_side_outputs/README.md)

**What you'll learn:**
- Route events to multiple output streams
- Use OutputTag for type-safe routing
- Implement multi-way splits efficiently
- Separate alerts by severity and type

**Use cases:**
- Alert routing (LOW_STOCK, OUT_OF_STOCK, PRICE_DROP)
- Data quality splits (valid vs invalid records)
- Multi-level processing (bronze, silver, gold layers)
- Event categorization (errors, warnings, info)

**Difficulty:** Intermediate | **Estimated time:** 45 minutes

---

**Total Learning Time:** ~4 hours (including exercises)

## 🚀 Quick Start

### Prerequisites

```bash
# Required software:
- Java 11 (for Flink 1.20)
- Gradle 8.14+
- Docker & Docker Compose (for Kafka)
```

### Running Individual Patterns

Each pattern can be run standalone:

```bash
# Start Kafka (if needed)
docker compose up -d redpanda-0 redpanda-1 redpanda-2

# Build the project
./gradlew :flink-inventory:build

# Run Pattern 01: Hybrid Source
./gradlew :flink-inventory:run -PmainClass=com.ververica.composable_job.flink.inventory.patterns.hybrid_source.HybridSourceExample

# Run Pattern 02: Keyed State
./gradlew :flink-inventory:run -PmainClass=com.ververica.composable_job.flink.inventory.patterns.keyed_state.KeyedStateExample

# Run Pattern 03: Timers
./gradlew :flink-inventory:run -PmainClass=com.ververica.composable_job.flink.inventory.patterns.timers.TimerExample

# Run Pattern 04: Side Outputs
./gradlew :flink-inventory:run -PmainClass=com.ververica.composable_job.flink.inventory.patterns.side_outputs.SideOutputExample
```

## 📖 Learning Path

### 🎓 Recommended Learning Sequence

```
┌──────────────────────────────────────────────────────────────┐
│                    LEARNING JOURNEY                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Week 1: Hybrid Source (Foundational)                       │
│  ├─ Read README (30 min)                                    │
│  ├─ Run HybridSourceExample (15 min)                        │
│  └─ Complete exercises (30 min)                             │
│                                                              │
│  Week 2: Keyed State (Essential)                            │
│  ├─ Read README (45 min)                                    │
│  ├─ Run KeyedStateExample (15 min)                          │
│  └─ Complete exercises (60 min)                             │
│                                                              │
│  Week 3: Timers (Advanced Timing)                           │
│  ├─ Read README (45 min)                                    │
│  ├─ Run TimerExample (15 min)                               │
│  └─ Complete exercises (60 min)                             │
│                                                              │
│  Week 4: Side Outputs (Efficient Routing)                   │
│  ├─ Read README (30 min)                                    │
│  ├─ Run SideOutputExample (15 min)                          │
│  └─ Complete exercises (45 min)                             │
│                                                              │
│  Week 5: Integration & Production                            │
│  ├─ Study InventoryManagementJobRefactored                  │
│  ├─ Review PATTERN-COMPOSITION.md                           │
│  └─ Build your own inventory job                            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### For Self-Paced Learners

**Option 1: Fast Track (1 week)**
- Focus on running examples and reading key sections
- Skip detailed exercises
- Review quiz questions to test understanding

**Option 2: Deep Dive (4 weeks)**
- Complete all exercises with solutions
- Experiment with pattern modifications
- Build your own variations

**Option 3: Workshop Format (1 day)**
- Morning: Patterns 01 + 02 with exercises
- Afternoon: Patterns 03 + 04 with exercises
- Evening: Integration + your own job

---

## 📁 Module Structure

Each pattern module follows this consistent structure:

```
XX_pattern_name/
├── README.md                    # Comprehensive learning guide
│   ├── 🎯 Learning Objectives
│   ├── 📖 Pattern Overview
│   ├── 🔑 Key Concepts
│   ├── 💻 Code Examples
│   ├── 🎓 Hands-On Exercises (3)
│   ├── ❌ Common Pitfalls
│   ├── 📊 Performance Tips
│   └── ✅ Quiz
├── {Pattern}Example.java        # Standalone runnable example
└── solution/                    # Exercise solutions (if applicable)
```

### Why This Structure?

- **Isolation:** Each pattern can be learned independently
- **Progression:** Builds from simple to complex concepts
- **Practice:** Exercises reinforce learning
- **Production:** Real-world tips and pitfalls

---

## 📖 Pattern Details

### 01. Hybrid Source (Bounded → Unbounded)

**Use Case:** Load product catalog from file, then stream updates from Kafka

**What You'll Learn:**
- Creating HybridSource with FileSource + KafkaSource
- Seamless source switching
- Custom StreamFormat for file reading
- When to use hybrid vs separate jobs

**Key Flink APIs:**
```java
HybridSource.builder(fileSource)
    .addSource(kafkaSource)
    .build()
```

**Real-World Example:**
Initialize recommendation model from historical purchases (file), then update with live purchases (Kafka).

---

### 02. Keyed State (Per-Key State Management)

**Use Case:** Track inventory changes per product

**What You'll Learn:**
- ValueState for single values per key
- State initialization in open()
- State updates and checkpointing
- State TTL and cleanup

**Key Flink APIs:**
```java
ValueState<Product> state = getRuntimeContext()
    .getState(new ValueStateDescriptor<>("last-product", Product.class));
```

**Real-World Example:**
Track user session data, device telemetry, or account balances with fault-tolerant state.

---

### 03. Timers (Timeout Detection)

**Use Case:** Detect products with no updates for 1 hour

**What You'll Learn:**
- Processing time vs Event time timers
- Registering timers in processElement()
- onTimer() callback handling
- Timer cleanup and state management

**Key Flink APIs:**
```java
ctx.timerService().registerProcessingTimeTimer(timestamp);

@Override
public void onTimer(long timestamp, OnTimerContext ctx, Collector out) {
    // Timer fired - emit alert
}
```

**Real-World Example:**
Session timeouts, SLA monitoring, scheduled tasks, or fraud detection time windows.

---

### 04. Side Outputs (Multi-way Routing)

**Use Case:** Route low-stock alerts, out-of-stock events, and price drops separately

**What You'll Learn:**
- OutputTag creation with type safety
- Emitting to multiple outputs
- Accessing side output streams
- Performance vs multiple filters

**Key Flink APIs:**
```java
OutputTag<AlertEvent> alertTag = new OutputTag<AlertEvent>("alerts") {};
ctx.output(alertTag, alert);
DataStream<AlertEvent> alerts = mainStream.getSideOutput(alertTag);
```

**Real-World Example:**
Error handling, A/B testing splits, alert routing, or dead letter queues.

---

## 🔧 Shared Components

The `shared/` directory contains reusable components used across patterns:

```
shared/
├── model/              # Data models
│   ├── InventoryEvent.java    # Main event type (immutable, builder pattern)
│   ├── EventType.java          # Enum for event types
│   └── AlertEvent.java         # Alert events for side outputs
│
├── config/             # Configuration
│   ├── InventoryConfig.java   # Main config (builder pattern)
│   ├── KafkaTopics.java        # Topic name constants
│   └── StateConfig.java        # State backend & TTL config
│
└── processor/          # Reusable processors
    ├── ProductParser.java      # JSON → Product parsing
    └── SinkFactory.java        # Kafka sink creation
```

### Using Shared Components

```java
// Configuration
InventoryConfig config = InventoryConfig.fromEnvironment();

// Models
InventoryEvent event = InventoryEvent.builder()
    .productId("P001")
    .eventType(EventType.INVENTORY_DECREASED)
    .build();

// Constants
kafkaSink.setTopic(KafkaTopics.INVENTORY_EVENTS);
```

---

## 🎓 Workshop Exercises

### Exercise Track A: Follow the Modules (Recommended)

Complete patterns in order:
1. **Day 1 Morning:** Pattern 01 (Hybrid Source)
2. **Day 1 Afternoon:** Pattern 02 (Keyed State)
3. **Day 2 Morning:** Pattern 03 (Timers)
4. **Day 2 Afternoon:** Pattern 04 (Side Outputs)
5. **Day 3:** Combine all patterns in main job

### Exercise Track B: Build Your Own (Advanced)

Create a **real-time warehouse system**:
- Track inventory across 3 warehouses
- Alert when total inventory < 10
- Auto-reorder when stock low for 2+ hours
- Route urgent alerts vs standard alerts
- Bootstrap from warehouse database dumps

**Hint:** Combine all 4 patterns!

---

## 📊 Architecture Overview

### How Patterns Combine

```
┌─────────────────────────────────────────────────────────────┐
│                    Inventory Management Job                  │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Pattern 01: Hybrid Source]                                │
│  ┌─────────────┐          ┌─────────────┐                  │
│  │ File Source │─────────▶│ Hybrid      │                  │
│  └─────────────┘          │ Source      │                  │
│  ┌─────────────┐          └──────┬──────┘                  │
│  │Kafka Source │─────────────────┘                         │
│  └─────────────┘                                            │
│         │                                                    │
│         ▼                                                    │
│  [Parse Products]                                           │
│         │                                                    │
│         ▼                                                    │
│  [Pattern 02: Keyed State + Pattern 03: Timers]           │
│  ┌──────────────────────────────────────┐                  │
│  │ KeyedProcessFunction                  │                  │
│  │ - ValueState (last product)          │                  │
│  │ - Timer (stale detection)            │                  │
│  │ - Track changes per product          │                  │
│  └──────────┬──────────────┬────────────┘                  │
│             │              │                                 │
│             ▼              ▼                                 │
│     [Main Output]   [Pattern 04: Side Outputs]             │
│     InventoryEvent   ┌──────────┬──────────┬──────────┐    │
│                      │          │          │          │    │
│                      ▼          ▼          ▼          ▼    │
│                   LowStock  OutOfStock  PriceDrop  Errors │
│                                                               │
│  [Sinks]                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                 │
│  │  Kafka   │  │  Kafka   │  │WebSocket │                 │
│  │ Events   │  │ Alerts   │  │  Fanout  │                 │
│  └──────────┘  └──────────┘  └──────────┘                 │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🧪 Testing Your Knowledge

After completing all modules, you should be able to answer:

1. **When** would you use a Hybrid Source vs two separate jobs?
2. **Why** is keyed state automatically partitioned by Flink?
3. **How** do timers survive job failures and restarts?
4. **What** is the performance difference between side outputs and multiple filters?

**Bonus:** Can you explain how checkpointing works across all these patterns?

---

## 📚 Additional Resources

### Flink Documentation
- [DataStream API](https://nightlies.apache.org/flink/flink-docs-release-1.20/)
- [State & Fault Tolerance](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/dev/datastream/fault-tolerance/state/)
- [Event Time & Watermarks](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/concepts/time/)

### Ververica Resources
- [Ververica Platform](https://www.ververica.com/)
- [Flink Training](https://www.ververica.com/academy)
- [Ververica Blog](https://www.ververica.com/blog)

### Community
- [Apache Flink Slack](https://flink.apache.org/community.html)
- [Flink Forward Conference](https://www.flinkforward.org/)

---

## 🎯 Next Steps

After mastering these inventory patterns, continue to:

### Recommendations Module
- **Session Windows** - Group events by user session
- **Broadcast State** - Distribute ML patterns to all tasks
- **CEP** - Complex event patterns for cart abandonment
- **Paimon Integration** - Unified batch-stream storage

### Advanced Topics
- **Exactly-Once Semantics** - End-to-end guarantees
- **Savepoints** - Job upgrades without data loss
- **Watermark Strategies** - Handling late data
- **Custom Operators** - Building reusable components

---

## 🎯 Summary

### Key Takeaways

After completing these 4 foundational patterns, you will understand:

✅ **Hybrid Sources** - Bootstrap from bounded data → stream from unbounded
✅ **Keyed State** - Manage per-key fault-tolerant state
✅ **Timers** - Implement timeout detection and scheduled processing
✅ **Side Outputs** - Route events efficiently to multiple streams
✅ **Pattern Composition** - Combine patterns for production jobs

---

## 🚀 Next Steps

After mastering these foundational patterns:

1. **📚 Learn Advanced Patterns**
   - [Recommendation Patterns](../../../flink-recommendations/src/main/java/com/ververica/composable_job/flink/recommendations/patterns/README.md)
   - Pattern 05: Session Windows (user sessions)
   - Pattern 06: Broadcast State (ML model distribution)
   - Pattern 07: CEP (complex event detection)

2. **🔗 Study Pattern Composition**
   - Review [InventoryManagementJobRefactored.java](../InventoryManagementJobRefactored.java)
   - Read [PATTERN-COMPOSITION.md](../PATTERN-COMPOSITION.md)
   - Understand how patterns combine in production

3. **🛠️ Build Your Own Job**
   - Apply patterns to your domain
   - Combine multiple patterns
   - Deploy to production

4. **🎓 Explore Advanced Topics**
   - State migration and schema evolution
   - Exactly-once semantics end-to-end
   - Apache Paimon integration
   - Performance tuning and optimization

---

## 🏆 Completion Checklist

Track your progress through the patterns:

**Pattern 01: Hybrid Source**
- [ ] Read README (45 min)
- [ ] Run HybridSourceExample
- [ ] Complete 3 exercises
- [ ] Pass quiz (5/5 correct)

**Pattern 02: Keyed State**
- [ ] Read README (60 min)
- [ ] Run KeyedStateExample
- [ ] Complete 3 exercises
- [ ] Pass quiz (5/5 correct)

**Pattern 03: Timers**
- [ ] Read README (60 min)
- [ ] Run TimerExample
- [ ] Complete 3 exercises
- [ ] Pass quiz (5/5 correct)

**Pattern 04: Side Outputs**
- [ ] Read README (45 min)
- [ ] Run SideOutputExample
- [ ] Complete 3 exercises
- [ ] Pass quiz (5/5 correct)

**Integration**
- [ ] Study InventoryManagementJobRefactored
- [ ] Understand pattern composition
- [ ] Build your own inventory job

**You're ready for production when you can:**
- ✅ Explain when to use each pattern
- ✅ Implement patterns from scratch
- ✅ Debug common state and watermark issues
- ✅ Optimize pattern performance
- ✅ Combine patterns for complex use cases

**Congratulations!** 🎉 You've mastered foundational Flink patterns.

---

## 🆘 Troubleshooting

### Build Issues

```bash
# Check Java version (must be 11 for Flink 1.20)
java -version

# Clean build
./gradlew clean build

# Rebuild specific module
./gradlew :flink-inventory:clean :flink-inventory:build
```

### Runtime Issues

```bash
# Check Kafka/Redpanda is running
docker compose ps

# View Kafka topics
docker exec -it redpanda-0 rpk topic list

# View logs
tail -f logs/inventory.log
```

### Pattern-Specific Help

See the README.md in each pattern directory for troubleshooting specific to that pattern.

---

## 📚 Further Reading

### Official Flink Documentation

- [Flink DataStream API](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/dev/datastream/overview/)
- [State & Fault Tolerance](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/dev/datastream/fault-tolerance/state/)
- [Timers & Process Functions](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/dev/datastream/operators/process_function/)
- [Side Outputs](https://nightlies.apache.org/flink/flink-docs-release-1.20/docs/dev/datastream/side_output/)

### Related Resources

- [PATTERN-COMPOSITION.md](../PATTERN-COMPOSITION.md) - How patterns compose in production
- [REFACTORING-SUMMARY.md](../../REFACTORING-SUMMARY.md) - Project transformation overview
- [Recommendation Patterns](../../../flink-recommendations/src/main/java/com/ververica/composable_job/flink/recommendations/patterns/README.md) - Advanced patterns

---

## 💬 Support & Community

### Getting Help

**For Pattern Questions:**
- Review README common pitfalls section
- Check Flink official documentation
- Search [Flink mailing list archives](https://flink.apache.org/community.html#mailing-lists)

**For Bugs:**
- File issue in project repository
- Include: Flink version, Java version, full error stack trace

**For Contributions:**
- Submit PRs with new exercises
- Share your own pattern variations
- Improve documentation with clarifications

---

<div align="center">
  <b>🎓 From Learning to Production</b><br>
  <i>Master patterns individually, then compose them into production systems</i>
</div>
