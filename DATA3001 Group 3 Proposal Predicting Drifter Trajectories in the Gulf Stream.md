# **Gulf Stream Drifter Forecasting — DATA3001 Group 3 Proposal**

## **1\. Research question and objectives**

Given a drifter's recent track in the Gulf Stream, where will it be 1 hour, 1 day and 1 week later? We will compare three forecasts on drifters held out from training: persistence (the drifter keeps its current velocity), the mean flow (it follows the time-averaged current), and a data-driven model that combines the drifter's recent motion with the local mean flow **\[CHECK: Leo\]**. We will also examine where forecasts fail, such as in meanders and for drifters that have lost their drogue **\[CHECK: Leo\]**.

## **2\. Background, existing studies and significance**

The Gulf Stream is the North Atlantic's western boundary current. It flows north along the US coast at up to about 2.5 m/s and leaves the coast near Cape Hatteras (NOAA). The jet is only tens of kilometres wide, so a drifter slightly off its core moves much more slowly. East of Hatteras, the current meanders and sheds rings, whose formation nearly doubled after 2000 (Gangopadhyay et al., 2019), which makes drifter paths hard to predict. Better short-term forecasts matter for search and rescue and oil-spill response, where the first 6–24 hours are critical (Révelard et al., 2021\) and an object can travel about 170 km in a day.

Past work shows simple baselines are hard to beat. In the Gulf of Mexico, basic neural networks failed to outperform autoregression (Grossi et al., 2025), and in the Kuroshio, the Pacific counterpart of the Gulf Stream, the best method changed with flow curvature (Lin et al., 2024). No study we found compares these baselines with a learned model on held-out hourly GDP drifters in the Gulf Stream. Our method needs only drifter data and no ocean model, so it could be reused in any region the GDP covers.

## **3\. Data, region and data adequacy**

**Data and scope.** We use the NOAA Global Drifter Program (GDP) hourly dataset (Elipot et al., 2016), accessed through CloudDrift, to assess data availability for forecasts at 1 hour, 1 day and 1 week. Our study window (25–45°N, 80–50°W) covers the Gulf Stream and neighbouring waters rather than defining the current's exact boundary. The extracted subset contains 8,138,225 records from 1,481 drifters, spanning November 1990 to October 2022\. Available fields include position, time, velocity, sea surface temperature (SST) and quality indicators.

**Processing and quality.** Continuous segments require the same drifter and exactly one-hour intervals, so no forecast window crosses a gap or a regional exit. The subset has no missing timestamps, duplicate drifter–time pairs or missing velocities; SST is 3.39% missing and optional. Uncertainty fields and quality flags still require screening.

**Data adequacy.** We identify 5,725 continuous segments with a median duration of 288 hours (12 days). Using 24 hours of history, 1,465, 1,455, and 1,394 drifters provide eligible windows for 1-hour, 24-hour and 168-hour forecasts. The one-week task offers 7,403,575 overlapping candidate origins. These counts support data feasibility, not independent sample size or prediction accuracy.

*Figure 1\. Left: trajectories of the 100 drifters with the most regional observations (not a random sample). Right: continuous duration distribution with 24- and 168-hour reference lines. A 24-hour history plus a one-week forecast requires 192 hours.*

## **4\. Proposed method**

We will predict the future location of individual drifters at three forecast horizons: 1 hour, 24 hours and 1 week, using the previous 24 hours of each drifter's track **\[CHECK: Leo\]**. Three methods will be compared. Present velocity assumes the drifter continues at its current speed and direction. Spatial mean flow moves the drifter with the average velocity of all drifters observed nearby. Gradient boosting learns from the recent trajectory, including location, velocity and previous movement patterns **\[CHECK: Leo — add local mean flow?\]**.

The data will be split by drifter ID, with 80% of drifters used for training and 20% for testing, so test drifters never appear in training. Models will be evaluated using great-circle distance error in kilometres, reporting the mean, median and 90th percentile at each horizon.

**Product.** Our final product is a forecast evaluation on held-out Gulf Stream drifters: one figure of distance error against lead time for our model and both baselines on the same axes, with the train/test split and model development stated plainly. We will add example maps comparing each method's forecast track with a real drifter's path, showing where each method succeeds or fails.

## **5\. Preliminary forecasting results**

On a sample of 100 drifters, we evaluated 90,445 prediction points from 18 held-out drifters. Present velocity was slightly better at 1 hour (mean error 0.22 km vs 0.23 km for gradient boosting). At longer horizons, gradient boosting performed better: mean error of 11 km at 24 hours and 95 km at 1 week, compared with 16 km and 168 km for present velocity. This suggests persistence suits very short-term prediction, while a learned model is more useful for longer-term forecasting. The full regional dataset will be analysed in the final project.

*Figure 2\. Median distance error against forecast horizon for present velocity, spatial mean flow and gradient boosting, on 18 held-out drifters.*

## **6\. Timeline and work allocation**

The project runs from this proposal (week 4\) to the final report, with the full-data evaluation complete by week 8\.

| Week | Milestone | Main work |
| :---- | :---- | :---- |
| 4 | Proposal / README due | Research question, data-adequacy check, baseline results on sample data |
| 5 | Poster session | Poster built from proposal figures; feedback from client and peers |
| 6–7 | Full-data modelling | Run baselines and gradient boosting on full regional data; tune features |
| 8 | Evaluation | Error vs lead time on held-out drifters, more lead times, uncertainty |
| 9 | Draft results | Final figures; example drifter track maps; begin report |
| 10 | Presentation | Group presentation of results |
| Exam period | Final report | Full write-up, including reflection on AI use |

| Member | Role | Contribution |
| :---- | :---- | :---- |
|  |  |  |
|  |  |  |
|  |  |  |
|  |  |  |

## **References**

* Elipot, S. et al. (2016). A global surface drifter data set at hourly resolution. *Journal of Geophysical Research: Oceans* 121\. NOAA GDP hourly data accessed via CloudDrift, [doi:10.25921/x46c-3620](https://doi.org/10.25921/x46c-3620).  
* Gangopadhyay, A. et al. (2019). An observed regime shift in the formation of warm core rings from the Gulf Stream. *Scientific Reports* 9, 12319\. [link](https://www.nature.com/articles/s41598-019-48661-9)  
* Grossi, M. D., Jegelka, S., Lermusiaux, P. F. J. & Özgökmen, T. M. (2025). Surface drifter trajectory prediction in the Gulf of Mexico using neural networks. *Ocean Modelling* 196\. [link](https://www.sciencedirect.com/science/article/abs/pii/S1463500325000460)  
* Lin, H., Yu, W. & Lian, Z. (2024). Influence of ocean current features on the performance of machine learning and dynamic tracking methods in predicting marine drifter trajectories. *Journal of Marine Science and Engineering* 12(11), 1933\. [doi](https://doi.org/10.3390/jmse12111933)  
* NOAA National Ocean Service. How fast is the Gulf Stream? [link](https://oceanservice.noaa.gov/facts/gulfstreamspeed.html)  
* Révelard, A. et al. (2021). Sensitivity of skill score metric to validate Lagrangian simulations in coastal areas: recommendations for search and rescue applications. *Frontiers in Marine Science* 8\. [doi](https://doi.org/10.3389/fmars.2021.630388)