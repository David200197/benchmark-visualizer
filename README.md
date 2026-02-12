# Benchmark Framework Visualizer

Interactive single-file dashboard for visualizing [TechEmpower-style](https://www.techempower.com/benchmarks/) benchmark results. Load a `result.json`, explore framework performance through charts, tables, and rankings, then download a self-contained static report you can share with anyone — no server required.

## Features

- **Drag & drop** — Load your `result.json` and get an instant dashboard
- **Multi-test support** — JSON serialization and Plaintext test visualization with per-concurrency/pipeline breakdowns
- **Rich statistics per framework:**
  - Total requests and average req/s
  - % vs leader (how far behind the #1 framework)
  - Performance tier (S/A/B/C/D) based on throughput relative to the leader
  - Latency stability score derived from coefficient of variation (stdev/avg)
  - JSON vs Plaintext request split
  - Multiplier vs last place
  - SLOC count
- **Interactive charts** — Bar, line, radar, and doughnut charts powered by Chart.js
- **Ranking table** — Sortable with tier badges, req/s, % vs leader, and verification status
- **Relative performance bars** — Visual comparison ordered by ranking with leader percentages
- **Bilingual** — Full ES/EN language toggle (persisted via localStorage)
- **Static report download** — Exports a standalone HTML file with all data embedded; opens instantly in any browser with no loading, no upload step, and no download button
- **Zero dependencies** — Single HTML file, no build step, no server, no framework

## Usage

### Option A: Open directly

```
Open benchmark-visualizer.html in any modern browser
```

### Option B: Serve locally

```bash
# Python
python3 -m http.server 8080

# Node
npx serve .
```

Then navigate to `http://localhost:8080/benchmark-visualizer.html`.

### Loading data

1. Open the visualizer
2. Drag your `result.json` onto the upload zone, or click to browse
3. The dashboard renders immediately

Typical path for TechEmpower results:

```
~/FrameworkBenchmarks/results/result.json
```

### Downloading a static report

Once data is loaded, click **Download Static Report** at the bottom. The downloaded HTML file:

- Contains all benchmark data embedded as a JSON variable
- Loads instantly on open — no upload step, no spinner
- Has no download button (it's already the final report)
- Preserves the language you had selected
- Works fully offline

## Expected input format

The visualizer expects the standard TechEmpower Benchmark Framework `result.json` structure:

```json
{
  "uuid": "...",
  "name": "...",
  "environmentDescription": "...",
  "startTime": "2025-01-15T10:00:00Z",
  "completionTime": "2025-01-15T12:00:00Z",
  "duration": 15,
  "frameworks": ["express", "fastify", "elysia", "hono", "titanpl"],
  "concurrencyLevels": [16, 32, 64, 128, 256, 512],
  "pipelineConcurrencyLevels": [256, 1024, 4096, 16384],
  "operatingSystem": {
    "name": "Linux",
    "version": "6.1.0"
  },
  "git": {
    "repositoryUrl": "...",
    "branchName": "master",
    "commitId": "abc123..."
  },
  "verify": {
    "express": { "json": "pass", "plaintext": "pass" },
    "fastify": { "json": "pass", "plaintext": "pass" }
  },
  "rawData": {
    "json": {
      "express": [
        {
          "latencyAvg": "1.23ms",
          "latencyMax": "45.67ms",
          "latencyStdev": "0.89ms",
          "totalRequests": 234567,
          "startTime": 1705312800,
          "endTime": 1705312815
        }
      ]
    },
    "plaintext": {
      "express": [
        {
          "latencyAvg": "0.45ms",
          "latencyMax": "12.34ms",
          "latencyStdev": "0.23ms",
          "totalRequests": 1234567,
          "timeout": 0,
          "startTime": 1705312900,
          "endTime": 1705312915
        }
      ]
    },
    "slocCounts": {
      "express": 42,
      "fastify": 38
    }
  }
}
```

### Required fields

| Field | Type | Description |
|-------|------|-------------|
| `frameworks` | `string[]` | List of framework names |
| `rawData` | `object` | Contains `json` and/or `plaintext` test data |
| `rawData.json.<framework>` | `array` | One entry per concurrency level |
| `rawData.plaintext.<framework>` | `array` | One entry per pipeline level |

### Optional fields

| Field | Type | Description |
|-------|------|-------------|
| `uuid` | `string` | Test run identifier |
| `duration` | `number` | Seconds per test (default: 15) |
| `concurrencyLevels` | `number[]` | Labels for JSON test x-axis |
| `pipelineConcurrencyLevels` | `number[]` | Labels for Plaintext test x-axis |
| `verify` | `object` | Pass/fail status per framework per test |
| `rawData.slocCounts` | `object` | Lines of code per framework |
| `operatingSystem` | `object` | `{ name, version }` |
| `git` | `object` | `{ repositoryUrl, branchName, commitId }` |

### Latency format

Latency values accept multiple formats, automatically parsed:

- `"1.23ms"` → 1.23 ms
- `"450us"` → 0.45 ms
- `"0.5s"` → 500 ms
- `1.23` → 1.23 (raw number)

## Dashboard sections

### Overview tab

- **Metric cards** — UUID, framework count, total requests across all frameworks, best framework, best req/s, OS
- **Podium cards** — Top 5 frameworks with:
  - Tier badge (S/A/B/C/D)
  - % vs leader tag (color-coded: green ≤15% gap, amber ≤40%, red >40%)
  - Total requests, avg req/s, min latency
  - Stability gauge (100% = perfectly consistent latency)
  - JSON / Plaintext request split
  - Multiplier vs last place
  - SLOC count
- **Charts** — Total requests bar chart + average latency bar chart

### JSON Test tab

- Latency by concurrency level (line chart)
- Requests by concurrency level (grouped bar chart)
- Detailed data table with color-coded latency values

### Plaintext Test tab

- Latency by pipeline level (line chart)
- Requests by pipeline level (grouped bar chart)
- Detailed data table with timeout column

### Comparison tab

- Radar chart comparing latency ranges and request volumes
- Doughnut chart showing throughput distribution
- Relative performance bars ordered by ranking with % vs leader

### Rankings tab

- Full ranking table with position badge, tier, total requests, req/s, % vs leader, latency range, verification status, SLOC
- Test metadata table (UUID, git info, OS, dates, duration)

## Browser support

Works in all modern browsers: Chrome, Firefox, Safari, Edge. Requires JavaScript enabled. Chart.js is loaded from CDN (`cdn.jsdelivr.net`).

## License

MIT
