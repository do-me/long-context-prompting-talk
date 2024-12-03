# Long Context Prompting
This is the supporting repo for my talk on 4 December 2024 for GPT@JRC. 

It includes a practical example for efficient use of long-context models (>64.000 tokens like >Llama 3.1, Qwen 2.5 or commercial models like Gemini Pro 1.5 with 2M tokens).

The whole point of the presentation is that:
- you should make efficient use of the model's context length and provide as much information right in the prompt as you can
- you should be aware of a model's output quality which might dramatically decrease (https://github.com/NVIDIA/RULER)
 like when you use more than 64k tokens in Llama 3.1. Newer models & commercial models do not seem to have this problem anymore (https://blog.google/technology/ai/google-gemini-next-generation-model-february-2024/#performance)
- if you can fit all information in the prompt do so and do not use RAG: https://arxiv.org/abs/2407.16833
- if the prompt exceeds the model's context, summarize the data with LLMs and use LLMLingua2 to compress it further, but check the results! (https://huggingface.co/spaces/microsoft/llmlingua-2) Be careful when you enter a database with highly varying text lengths - long text entries might create a bias with respect to shorter
- if it's still to much information, you can use a hybrid approach as proposed by Google https://arxiv.org/abs/2407.16833