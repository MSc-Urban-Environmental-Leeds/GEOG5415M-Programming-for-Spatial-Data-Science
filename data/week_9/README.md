# Week 9 data: street-view images and socio-economic status in Amsterdam

These files are used in the Week 9 practical (*Can you see deprivation from the street?*). They are small
extracts from a larger research project. The full workflow, which downloads the images, calculates the
image embeddings and fits the research models, is in the
[StreetViewDeprivation-Amsterdam](https://github.com/nickmalleson/StreetViewDeprivation-Amsterdam)
repository. The method was first developed for Greater Manchester (Asher et al., 2027).

## Files

| File | Rows | What it contains |
|------|------|------------------|
| `amsterdam_buurten_2024.geojson` | 517 | Boundaries of the land *buurten* (neighbourhoods) in the municipality of Amsterdam. Columns: `buurtcode`, `buurt_name`, `population` (residents, CBS 2024). Coordinates are latitude and longitude (EPSG:4326). |
| `amsterdam_ses_2023.csv` | 420 | Socio-economic status score (SES-WOA) for each buurt in 2023. Columns: `buurtcode`, `ses_score` (total), and the three parts of the score: `ses_wealth`, `ses_education`, `ses_labour`. Higher scores mean higher socio-economic status. |
| `amsterdam_street_points.csv` | 30,155 | One row per street-view location (four photographs, facing north, east, south and west). Columns: `point_id`, `pano_id` (the City of Amsterdam's identifier for the panorama), `image_date` (year and month the photographs were taken), `latitude`, `longitude`, and one score for each text description in `prompts.csv`. |
| `prompts.csv` | 15 | The text descriptions that the images were compared with. `column` is the column name in `amsterdam_street_points.csv`; `prompt` is the text given to CLIP. |
| `amsterdam_buurt_clip_embeddings.csv` | 420 | The median CLIP image embedding of all the photographs in each buurt (512 numbers per buurt, columns `emb_000` to `emb_511`). |
| `amsterdam_buurt_alphaearth_embeddings.csv` | 517 | The median AlphaEarth satellite embedding (2023) of each buurt (64 numbers, columns `ae_00` to `ae_63`). Used in an optional extension. |
| `images.csv` | 108 | Index of the photographs in `images/`. `purpose` is `buurt` (six random photographs from each of eight buurten in Centrum and Zuidoost) or `prompt` (the four best-matching photographs in Amsterdam for each description, from different buurten). |
| `images/` | 107 | The photographs, reduced to 320 × 320 pixels. |
| `images_full_size/` | 4 | Four photographs at their original size (640 × 640 pixels), with their stored CLIP embeddings in `stored_clip_embeddings.csv`. Used in an optional extension. |

## Sources and licences

| Data | Publisher | Licence | Attribution |
|------|-----------|---------|-------------|
| Street-level panoramas ("Kernregistratie panoramabeelden") | Gemeente Amsterdam, via `https://api.data.amsterdam.nl/panorama/` | CC-BY 4.0 | Contains data from Gemeente Amsterdam, licensed under CC-BY 4.0. Faces and number plates are blurred by the publisher. |
| SES-WOA scores, StatLine table 86092NED (*regio-indeling 2024*), reporting year 2023 | Centraal Bureau voor de Statistiek (CBS) | CC-BY 4.0 | Source: CBS, StatLine table 86092NED. CBS marks the most recent year as provisional. |
| Buurt boundaries, Wijk- en Buurtkaart 2024, via PDOK | CBS | CC-BY 4.0 | Source: CBS. |
| AlphaEarth Foundations Satellite Embedding, annual 2023 | Google and Google DeepMind, via Google Earth Engine | CC-BY 4.0 | The AlphaEarth Foundations Satellite Embedding dataset is produced by Google and Google DeepMind. |
| CLIP ViT-B/32 model | OpenAI (Radford et al., 2021) | MIT | Used to calculate the image and text embeddings. |

The boundaries and SES scores were downloaded on 16 June 2026 and the panoramas by 26 June 2026 (from the
file dates of the research data).

## How these files were made

1. The research workflow sampled about 50,000 panorama locations across Amsterdam and three neighbouring
   municipalities, cut each panorama into four 640 × 640 pixel photographs, and calculated a CLIP embedding
   for each photograph. Only the 30,155 locations inside the municipality of Amsterdam are included here.
2. **Description scores.** For each photograph, the cosine similarity between its CLIP image embedding and
   the CLIP text embedding of each description was calculated. The four photographs at a location were
   averaged. Each score was then standardised across all Amsterdam locations (mean 0, standard deviation 1),
   so a score of 0 means "as similar to the description as an average Amsterdam location" and a score of 2
   means "two standard deviations more similar than average".
3. **Buurt embeddings** are the element-wise median of the embeddings of all photographs in each buurt
   (notebook 3 of the research repository).
4. **AlphaEarth embeddings** are the median of the 10 m pixels inside each buurt (notebook 9a of the
   research repository).
5. Column names were translated into English. The photographs were selected with a fixed random seed (42).

## References

Asher, M., Comber, A., Golzari Osguie, N., Bui Quang, T., Kieu, M. and Malleson, N. 2027. Estimating visible
deprivation through the analysis of street view image embeddings. *Computers, Environment and Urban
Systems*. **131**, 102526. https://doi.org/10.1016/j.compenvurbsys.2026.102526

Radford, A. et al. 2021. Learning transferable visual models from natural language supervision.
*Proceedings of the 38th International Conference on Machine Learning*. PMLR 139, pp. 8748–8763.
