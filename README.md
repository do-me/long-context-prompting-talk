# LLM Long Context Prompting
This is the supporting repo for my talk on 4 December 2024 for GPT@JRC. 

It includes a practical example for efficient use of long-context models (>64,000 tokens like >Llama 3.1, Qwen 2.5, or commercial models like Gemini Pro 1.5 with 2M tokens).

## Conclusion

The whole point of the presentation is that:
- you should make efficient use of the model's context length and provide as much information right in the prompt as you can
- you should be aware of a model's output quality which might dramatically decrease [as detailed here](https://github.com/NVIDIA/RULER) like when you use more than 64k tokens in Llama 3.1. Newer models & commercial models do not seem to have this problem anymore [as described in Google's February 2024 blog](https://blog.google/technology/ai/google-gemini-next-generation-model-february-2024/#performance)
- if you can fit all information in the prompt, do so and [do not use RAG](https://arxiv.org/abs/2407.16833) for improved quality
- if the prompt exceeds the model's context, summarize the data with LLMs and use [LLMLingua2](https://huggingface.co/spaces/microsoft/llmlingua-2) to compress it further, but check the results! Be careful when you enter a database with highly varying text lengths - long text entries might create a bias with respect to shorter ones
- if it's still too much information, you can use a hybrid approach [as proposed by Google](https://arxiv.org/abs/2407.16833)

## Example Use Case

The European Commission wants to reduce the [reporting burden](https://commission.europa.eu/system/files/2023-10/Factsheet_CWP_Burdens_10.pdf) by 25%. Earth Observation can contribute especially to this bullet: 

> To revise the Regulation on European statistics, aimed at reducing the number of surveys and
increasing the use of automated and simplified processes, which will bring cost savings – including for
SMEs – of an estimated €450 million.

The idea here is to create a first automatic link between [European legislation](https://eur-lex.europa.eu/homepage.html?locale=en) and e.g. [Copernicus products](https://www.copernicus.eu/en/accessing-data-where-and-how/copernicus-services-catalogue).

If you are part of an EU institution, please feel free to reach out.

## Example output 

Run with Llama 3.1 70B Instruct with [this prompt](https://github.com/do-me/long-context-prompting-talk/blob/main/complete_prompt.txt), consisting of:

1. a main prompt

> I will give you a piece of legislation and a list of Copernicus Land products.
Extract all reporting obligations in the paragraphs and create a table linking the most suitable Land products to the paragraphs.
The output table should contain following columns:
Reporting obligation summary, reporting obligation citation, Land product title, land product url.

2. [the water framework directive from EUR-LEX](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32000L0060)
3. [Copernicus Land products](https://github.com/do-me/long-context-prompting-talk/blob/main/copernicus_land_products.txt)

| Reporting Obligation Summary | Reporting Obligation Citation | Land Product Title | Land Product URL |
| --- | --- | --- | --- |
| Analyze characteristics of river basin district, review impact of human activity, and conduct economic analysis of water use | Article 5 | CLC+Backbone 2018 (raster 10 m), Europe, 3-yearly | https://land.copernicus.eu/en/products/clc-backbone/clc-backbone-2018 |
| Monitor water status in river basin district | Article 8 | Imperviousness Density 2018 (raster 10 m and 100 m), Europe, 3-yearly | https://land.copernicus.eu/en/products/high-resolution-layer-imperviousness/imperviousness-density-2018 |
| Monitor surface water status | Article 8 | High Resolution Image Mosaic 2018 True Colour (raster 10 m), Europe, 3-yearly | https://land.copernicus.eu/en/products/european-image-mosaic/high-resolution-image-mosaic-2018-true-colour-10m |
| Monitor groundwater status | Article 8 | European Ground Motion Service: Basic 2019-2023 (vector), Europe, yearly | https://land.copernicus.eu/en/products/european-ground-motion-service/egms-basic |
| Identify waters used for drinking water abstraction | Article 7 | Water Bodies 2020-present (raster 100 m), global, monthly – version 1 | https://land.copernicus.eu/en/products/water-bodies/water-bodies-global-v1-0-100m |
| Develop river basin management plan | Article 13 | CORINE Land Cover 2018 (vector/raster 100 m), Europe, 6-yearly | https://land.copernicus.eu/en/products/corine-land-cover/clc2018 |
| Consult public on river basin management plan | Article 14 | Urban Atlas Land Cover/Land Use 2018 (vector), Europe, 6-yearly | https://land.copernicus.eu/en/products/urban-atlas/urban-atlas-2018 |
| Report to Commission on river basin management plan | Article 15 | Normalised Difference Vegetation Index 2014-2020 (raster 300 m), global, 10-daily – version 1 | https://land.copernicus.eu/en/products/vegetation/normalized-difference-vegetation-index-300m-v1.0 |
| Establish environmental quality standards for priority substances | Article 16 | Water Bodies 2020-present (raster 100 m), global, monthly – version 1 | https://land.copernicus.eu/en/products/water-bodies/water-bodies-global-v1-0-100m |
| Report to Commission on implementation of Directive | Article 18 | High Resolution Image Mosaic 2018 True Colour (raster 10 m), Europe, 3-yearly | https://land.copernicus.eu/en/products/european-image-mosaic/high-resolution-image-mosaic-2018-true-colour-10m |
