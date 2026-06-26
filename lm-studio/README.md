# LM Studio documentation
Machine: `M4 MacBook Air with 16 GB of unified memory, 120 GB/s memory bandwidth`

## Important notes

* Use recommended sampling parameters
	* `temperature=1.0`
	* `top_p=0.95`
	* `top_k=64`
* for LM Studio, reasoning section parsing, gemma4 uses:
	* `<|channel>thought` as start string
	* `<channel|>` as end string
* to enable thinking, include `<|think|>` at the beginning of the system prompt
	* example: `"<|think|> You are a helpful assistant who prioritizes direct and concise responses."`

## Load configuration

* choose any context length, a good small amount is 4090 tokens
* full GPU offload (35 layers)
* [experimental] evaluation batch size (pre-PP) and physical batch size (PP) set to `512 tokens`
* max concurrent predictions, set to 1 if no parallel processing is desired, otherwise, 2-4 is great
	* enable unified KV cache if using parallel
* offload KV cache to GPU memory
* keep model in memory:
	* llama.cpp's `--mlock` flag, use if under ~70% of Mac's unified memory
* Use `mmap` (memory mapping), supposedly is recommended at all times
* CPU Thread Pool Size: set to 4 (1 for each performance CPU core); compute is performed on GPU, but CPU cores supposedly orchestrate other matters (LM Studio forbids <1 CPU pool size)

## Flash attention
> *"supposedly there isn't a Metal shader for asymmetric KV cache quantization on the GPU simultaneously with flash attention on (because V cache quant requires fa in lms)"*
> *"yeah so that means there isnt a metal compute shader that is flash attention with K @ Q8 and V @ Q4*"

* Flash attention is recommended for memory savings and latency reduction
* From LM Studio, V cache quantization requires flash attention
	* Must use symmetric KV cache quantization if quantizing KV cache
		* K is more sensitive to compression than V
	* use `q8_0` at a minimum for K, and must match for V

## Model details relative to this documentation

Model: `google/gemma-4-E2B-it-qat-q4_0-gguf`
Size on disk: `3.35 GB`
