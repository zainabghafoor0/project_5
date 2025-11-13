📦 Batch-then-Drain Priority Queue Profile
Empirical Analysis Project — Priority Queue Study

This project extends the existing Huffman profile analysis by adding a new workload called Batch-then-Drain. The goal is to:

Generate traces for a new profile

Replay them using the existing harness

Time four different priority queue implementations

Produce CSV results

Visualize performance using the provided HTML tool

Write a short analytical report

This README guides you through every step required to reproduce the full pipeline.

🏗 0. Directory Setup (Required by Assignment)

As required, a new directory was created for the Batch-then-Drain generator:

src/trace-generators/batch_then_drain/


Inside it, a new file was added:

src/trace-generators/batch_then_drain/main.cpp


This file implements the new batch-then-drain workload, which consists of:

N inserts, followed by

N extract operations

Keys are drawn from a wide random range to minimize duplicates.
Generated traces are placed under:

traces/batch_then_drain/

⚙️ 1. Building the Project
1.1 Build the Batch-then-Drain Trace Generator

Compile manually using:

g++ -std=c++20 \
    src/trace-generators/batch_then_drain/main.cpp \
    utils/TraceConfig.cpp \
    -o batch_then_drain_trace


This creates:

batch_then_drain_trace

1.2 Build the Harness (Manual g++ Build)
g++ -std=c++20 \
    src/harness/main.cpp \
    utils/TraceConfig.cpp \
    utils/comparator.cpp \
    src/implementations/LinearBaseLine/LinearBaseLine.cpp \
    src/implementations/BinaryHeapInVector/BinaryHeapInVector.cpp \
    src/implementations/BinomialQueues/BinomialQueue.cpp \
    src/implementations/BinomialQueues/BQnode.cpp \
    src/implementations/BinomialQueues/SmallIntMixedOperations.cpp \
    src/implementations/BinomialQueues/InvertedListDictionary.cpp \
    src/implementations/Oracle/QuadraticOracle.cpp \
    -o harness


This produces:

harness

📜 2. Generate Batch-then-Drain Traces

From the project root, run:

./batch_then_drain_trace


This produces trace files such as:

traces/batch_then_drain/
    batch_then_drain_N_13_S_23.trace
    batch_then_drain_N_1024_S_23.trace
    batch_then_drain_N_2048_S_23.trace
    ...
    batch_then_drain_N_1048576_S_23.trace


Trace format:

Header: <profileName> <N> <seed>

N insert operations

N extract operations

🧪 3. Run the Harness (Replay + Timing)

Replay all traces through all four PQ implementations:

./harness batch_then_drain > csvs/batch_then_drain_results.csv


This will:

Load and replay traces

Apply warm-up + median timing

Benchmark:

Linear baseline

Binary heap

Binomial queue

Oracle (quadratic)

Save results into a single CSV

Preview the CSV:

head csvs/batch_then_drain_results.csv


Expected schema:

profile,N,seed,impl,time_usec
batch_then_drain,1024,23,LinearBaseLine,12345
batch_then_drain,1024,23,BinaryHeapInVector,2345
...

📊 4. Generate Performance Plots

Use the provided HTML visualization tool:

Open:

charts/pq_multi_impl_anchor_heap_tooltips.html


Inside the browser:

Load file → csvs/batch_then_drain_results.csv

The chart displays timing curves for all implementations

Export plot → save as:

charts/batch_then_drain_plot.png


This image will be included in your report.

📝 5. Write the Report

Create:

report_batch_then_drain.md

report_batch_then_drain.pdf

Your report should include:

✔ Introduction / Question

What you are analyzing & why.

✔ Method

Same Ns and seeds as Huffman

New workload: N inserts then N extracts

Same replay + timing procedure using the harness

Four implementations compared

✔ Results

Insert your batch_then_drain_plot.png

Add a 1–2 sentence summary

✔ Interpretation

Explain the curves using complexity theory:

Heap + Binomial → O(N log N)

Linear → quadratic behavior

Oracle → extremely slow for large N

Why the batch-then-drain pattern magnifies these differences

📁 6. Final Submission Checklist

Your final GitHub repository must include:

Code

src/trace-generators/batch_then_drain/main.cpp

All PQ implementations

Updated src/harness/main.cpp

Data

traces/batch_then_drain/*.trace

csvs/batch_then_drain_results.csv

Plot

charts/batch_then_drain_plot.png

Reports

report_batch_then_drain.md

report_batch_then_drain.pdf

Documentation

README.md (this file)

🔄 7. Re-running the Entire Pipeline (Clean Run)
rm -rf traces/batch_then_drain/*
rm -f csvs/batch_then_drain_results.csv

./batch_then_drain_trace
./harness batch_then_drain > csvs/batch_then_drain_results.csv
