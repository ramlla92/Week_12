# Sources

## Papers

1. **Pope et al., "Efficiently Scaling Transformer Inference" (MLSys 2023)**  
   https://arxiv.org/abs/2211.05102  

   This paper explains the systems-level challenges of transformer inference, including why autoregressive decoding is inherently sequential and how latency arises during token generation. It informed my understanding of why decode latency scales with output tokens and cannot be parallelized.

2. **Dao et al., "FlashAttention: Fast and Memory-Efficient Exact Attention" (NeurIPS 2022)**  
   https://arxiv.org/abs/2205.14135  

   This paper explains how attention computation scales with input length and how memory access patterns impact performance. It helped ground my understanding of why large-context prompts significantly increase prefill latency.

---

