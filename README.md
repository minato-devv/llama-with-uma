# Llama.cpp M4 Mac Settings Guide
Summary of optimal settings for running local LLMs on Apple Silicon (M4 MacBook Air) using llama.cpp.

## Key llama.cpp Flags:
* -ngl -1 (or -ngl 99): Offloads all layers to the GPU to utilize Unified Memory.
* -fa 1: Enables Flash Attention to save memory bandwidth during long conversations.
* -b 512: Sets batch size (optimized for M4 Air thermals).
* -ub 512: Sets micro-batch size.
* --cache-type-k q8_0: Quantizes the KV cache to compress conversational memory.

## Quick Tips:
* Metal is now enabled by default when building on macOS.
* Memory bandwidth (120 GB/s on M4 Air) is the main bottleneck for small models, so keep batch sizes moderate to prevent thermal throttling.
* Use MLX for raw speed on small models (<12B) if you don't need massive context windows.


## Additional Important Settings:
* K vs V Quantization: The Key (K) cache is more sensitive to quantization than the Value (V) cache. Use K8V4 (--cache-type-k q8_0 --cache-type-v q4_0) for optimal memory savings without severe quality degradation.
* Memory Mapping (--mmap vs --mlock): --mmap is enabled by default and recommended for Apple Silicon. Only use --mlock if the model fits comfortably within 70% of your total RAM to avoid system freezing.
* Mixture of Experts (--cmoe): Avoid using -cmoe or -ncmoe flags on Apple Silicon. Because of Unified Memory, forcing expert layers onto the CPU only slows down processing without saving memory.
