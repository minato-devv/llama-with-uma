# Llama.cpp M4 Mac Settings Guide

Notes taken by Siri AI via Apple Intelligence

## Key llama.cpp Flags:
* -ngl -1 (or -ngl 99): Offloads all layers to the GPU to utilize Unified Memory.
* -fa 1: Enables Flash Attention to save memory bandwidth during long conversations.
* -b 512: Sets batch size (optimized for M4 Air thermals).
* -ub 512: Sets micro-batch size.
* -c (Context Size): Practical sizes like 8192 or 16384 are recommended for interactive chat to save memory.
* --parallel: Sets the number of concurrent request slots.
* --cache-reuse: Speeds up repeated prompts by reusing the cache.

## Memory & Cache Management:
* K vs V Quantization: The Key (K) cache is more sensitive to quantization than the Value (V) cache. Use K8V4 (--cache-type-k q80 --cache-type-v q40) for optimal memory savings without severe quality degradation.
* Memory Mapping (--mmap vs --mlock): --mmap is enabled by default and recommended for Apple Silicon. Only use --mlock if the model fits comfortably within 70% of your total RAM to avoid system freezing.
* Mixture of Experts (--cmoe): Avoid using -cmoe or -ncmoe flags on Apple Silicon. Because of Unified Memory, forcing expert layers onto the CPU only slows down processing without saving memory.

## Quick Tips:
* Metal is now enabled by default when building on macOS.
* Memory bandwidth (120 GB/s on M4 Air) is the main bottleneck for small models, so keep batch sizes moderate to prevent thermal throttling.
* Use MLX for raw speed on small models (<12B) if you don't need massive context windows.
limit_mb=N: A macOS kernel parameter that is crucial for large model inference to prevent latency cliffs.


## Advanced Performance & Server Settings [experimental]:
- --spm-infill: Enable for better performance on code completion tasks if supported by the model.
- --ctx-size 0: Avoid using 0 (which defaults to the model's maximum) as it can consume excessive memory immediately.
- --priority: Sets the scheduling priority of the process (0-3). Setting to 2 reduces the chance of inference threads being interrupted on macOS.
- OLLAMA_NUM_PARALLEL: If using Ollama, set this to 1 for single-user local inference to maximize available memory.
- Quantization Baseline: Q4_K_M is generally considered the sweet spot for speed and quality on Apple Silicon.
