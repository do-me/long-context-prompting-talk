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

### 1 Reducing reporting burden in the water framework directive with Copernicus Land products
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

### 2 Attribute SDG indicators to the water framework directive

> I will give you a piece of legislation and a list of SDG indicators. Create a table with the top 10 most suitable SDG indicators and the grouped articles. Every article can be referenced multiple times.
The output table should contain following columns:
SDG indicator number, SDG indicator, Article numbers, reasoning

Then add the legislation text & the SDG indicators. Here is the [full prompt](https://github.com/do-me/long-context-prompting-talk/blob/main/sdg_attribution_prompt.txt).

Run with Llama 3.1 70B Instruct:

| SDG Indicator Number | SDG Indicator | Article Numbers | Reasoning |
| --- | --- | --- | --- |
| 6.3.1 | Proportion of domestic and industrial wastewater flows safely treated | Article 10, 11.3 | The legislation emphasizes the importance of treating wastewater to prevent pollution and protect the environment. |
| 6.4.1 | Change in water-use efficiency over time | Article 9, 11.3 | The legislation encourages water-use efficiency and the recovery of costs for water services. |
| 6.5.1 | Degree of integrated water resources management | Article 3, 4, 5 | The legislation promotes integrated water resources management and the coordination of measures at the river basin level. |
| 6.5.2 | Proportion of transboundary basin area with an operational arrangement for water cooperation | Article 3, 5 | The legislation emphasizes the importance of cooperation and coordination between Member States in transboundary river basins. |
| 6.6.1 | Change in the extent of water-related ecosystems over time | Article 4, 7 | The legislation aims to protect and enhance the aquatic environment and promote sustainable water use. |
| 6.a.1 | Amount of water- and sanitation-related official development assistance that is part of a government-coordinated spending plan | Article 9, 11.3 | The legislation encourages the provision of financial resources to support water and sanitation infrastructure. |
| 6.b.1 | Proportion of local administrative units with established and operational policies and procedures for participation of local communities in water and sanitation management | Article 14, 15 | The legislation promotes public participation and consultation in the development of river basin management plans. |
| 11.5.1 | Number of deaths, missing persons and directly affected persons attributed to disasters per 100,000 population | Article 4, 11.3 | The legislation aims to prevent and mitigate the effects of floods and droughts. |
| 12.4.1 | Number of parties to international multilateral environmental agreements on hazardous waste, and other chemicals that meet their commitments and obligations in transmitting information as required by each relevant agreement | Article 16, 17 | The legislation promotes the adoption of measures to prevent and control pollution of water. |
| 15.1.1 | Forest area as a proportion of total land area | Article 4, 7 | The legislation aims to protect and enhance the aquatic environment and promote sustainable water use, which is closely related to forest conservation. |
