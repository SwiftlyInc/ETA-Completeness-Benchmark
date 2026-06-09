# ETA-Completeness-Benchmark

A common methodology for measuring the completeness of real-time ETA data for public transit, created by Swiftly.

## Why does the ETA Completeness Benchmark exist?

Real-time information is only valuable if it actually reaches riders. Yet until now, there has been no standardized, industry-wide way to measure how often it doesn't.

The ETA Completeness Benchmark addresses this gap. While existing tools like the [ETA Accuracy Benchmark](https://github.com/TransitApp/ETA-Accuracy-Benchmark) measure the quality of predictions that *are* generated, they say nothing about the trips that silently disappear from real-time views entirely — the ghost buses and zombie buses that leave riders waiting with no information at all.

Building on [Cal-ITP](https://www.calitp.org/)'s existing real-time reporting framework (which has measured completeness for California agencies since 2022), the ETA Completeness Benchmark establishes the first standardized, industry-wide measure of how often riders receive *any* real-time information at all. It offers a neutral, transparent way for agencies to understand where their real-time systems succeed and where information silently disappears.

ETA completeness is one dimension of a three-part framework for evaluating real-time transit data quality:

- **Accuracy** — whether predictions are correct (see: [ETA Accuracy Benchmark](https://github.com/TransitApp/ETA-Accuracy-Benchmark))  
- **Completeness** — whether predictions exist at all *(this benchmark)*  
- **Accessibility** — whether real-time information reaches riders consistently across all passenger-facing channels

## What is ETA completeness?

ETA completeness measures **how fully a transit agency's real-time data captures what's happening on the ground.** It tracks not just how accurate predictions are, but whether riders receive any real-time information at all.

In practice, every scheduled trip-stop combination on a given service day falls into one of four categories:

|  | Ran as scheduled | Did not run as scheduled |
| :---- | :---- | :---- |
| **Reflected in real-time feed** | ✅ **Delivered \+ communicated** — The bus ran and riders saw it coming. This is the domain of traditional prediction accuracy. | ✅ **Undelivered \+ communicated** — The trip didn't run, but riders were correctly informed via a `CANCELED` or `SKIPPED` designation in the GTFS-rt feed. Not ideal, but keeps riders in the loop. |
| **Not reflected in real-time feed** | ❌ **Delivered \+ uncommunicated** — The bus ran, but wasn't reflected in real-time feeds. Riders had no way to know it was coming. These are "zombie buses." | ❌ **Undelivered \+ uncommunicated** — The trip didn't run *and* no real-time information was available. Apps like Google Maps, Transit, and Apple Maps default to showing scheduled times, even for trips that may not be coming. These are "ghost buses" — the most harmful outcome for the rider experience. |

Trip-stops where riders received reliable information — either a real-time prediction (category 1\) or a communicated disruption (category 3\) — are considered **complete**.

Trip-stops where real-time information went dark (categories 2 and 4\) are considered **incomplete**.

## How is the score calculated?

An agency's ETA completeness score is the percentage of scheduled trip-stop combinations that are "complete" over a given time period.

### Precise definition

A scheduled trip-stop combination is considered **complete** if it meets either of the following conditions:

1. The trip had an assigned vehicle **and** at least one prediction between 0–15 minutes out for that stop, OR  
2. The trip had a `CANCELED` designation for the trip ID, or a `SKIPPED` designation for the stop ID in the GTFS-rt trip updates feed.

All other scheduled trip-stop combinations are considered **incomplete**.

**Note:** Service alerts and trip modifications data are not included in this analysis. Both are valuable for passenger-facing tools and go beyond ETAs to provide contextual information on service changes and detours — but for simplicity, this benchmark focuses specifically on the completeness of ETA data in the trip updates feed.

### Formula

```
ETA Completeness Score = (Complete trip-stops) / (All scheduled trip-stops)
```

## What does "good" look like?

The following thresholds were established by analyzing transit data from hundreds of agencies and identifying natural patterns in completeness scores. "Best in class" is defined as a level of performance that is demonstrably achievable, with remaining categories built outward from that baseline.

| Score | Rating | What it means |
| :---- | :---- | :---- |
| ≥ 95% | 🟢 **Best in class** | Trips rarely slip through the cracks. ETAs reliably reach riders. |
| 90–94% | 🟡 **Good** | Gaps crop up occasionally, but real-time information is broadly reliable. |
| 85–89% | 🟠 **Fair** | Gaps are frequent enough to risk undermining rider confidence. |
| \< 85% | 🔴 **Needs improvement** | Missing data appears often enough to meaningfully disrupt how riders plan and perceive their trips. |

### Why the thresholds are tight

A completeness score of 88% might seem quite good — but it means *one in eight trips is missing real-time information entirely*. For a daily commuter, that's more than once a week waiting for a bus that never comes or arrives without notice. These thresholds reflect what's actually at stake for riders.

## How agencies can improve ETA completeness

The ETA Completeness Benchmark highlights the problem and also points toward solutions. The most effective strategies include:

**Improving vehicle-to-block assignment.** Automated assignment tools can significantly reduce trips that fall through the cracks due to operator log-in errors, last-minute vehicle swaps, and other common sources of unassigned service. When a vehicle is assigned to an otherwise-unassigned trip in operational software, ETAs are generated and riders see real information rather than a static scheduled time.

**Leveraging multiple AVL sources.** Redundant feeds ensure that if one system goes down, the data isn't lost entirely. Over any given 90-day period, one in four agencies experienced at least one GPS outage — illustrating the potential for substantial gains from redundancy.

**Feeding service adjustments directly into real-time systems.** When detours, closures, or cancellations happen, operational tools can automatically update rider-facing information to reflect temporary stops, cancellations, and detour paths — pushing `CANCELED` and `SKIPPED` designations to the GTFS-rt feed rather than leaving riders with stale scheduled times.

## Supporting organizations

See [Supporting organizations.md](Supporting%20organizations.md) for the full list of agencies and organizations that support this benchmark.

## Learn more

- 📖 [Introducing the ETA Completeness Benchmark](https://www.goswift.ly/blog/eta-completeness-benchmark) — Swiftly blog post with full methodology, performance tiers, and agency examples  
- 📊 [Cal-ITP real-time reporting](https://reports.dds.dot.ca.gov/gtfs_schedule/2025/10/235/index.html) — the statewide reporting program that inspired this benchmark  
- 🔗 [ETA Accuracy Benchmark](https://github.com/TransitApp/ETA-Accuracy-Benchmark) — the complementary benchmark for measuring prediction accuracy
