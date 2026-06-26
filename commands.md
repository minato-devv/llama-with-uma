./build/bin/llama-server \
	-hf deepreinforce-ai/Ornith-1.0-9B-GGUF:Q4_K_M \
	-ngl -1 \
	-ctk q8_0 \
	-ctv q4_0 \
	-fa on \
	-b 512 \
	-ub 512 \
	-c 32768
