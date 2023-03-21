# airQuality - Python Analysis
Air Quality Modeling and Forecasting

## Table of contents
* [General Info](#general-info)
* [Technologies](#technologies)
* [Processes and Features](#processes-and-features)
* [Conclusions](#conclusions)
* [Future Work](#future-work)
* [Data Citation](#data-citation)

## General Info
This project attempts to model and forecast average CO concentrations based on data from the UCI ML Repository.  The bulk of the project is in a Python Notebook.  This notebook serves as both a first foray into time-series analysis as well as a look at some new packages (e.g. hyperopt).

## Technologies
Project was created with a Python Notebook using Python version 3.9.13 in VSCode

The packages used can be found in the requirements.txt file
Packages of note:
-   pandas
-   numpy
-   re
-   matplotlib
-   sklearn
-   xgboost
-   hyperopt

## Processes and Features
Here I will outline how the analysis was completed.

First, the data was read in from the UCI ML Repository and saved locally as a csv.  The data was then read in and formatted using Pandas and re.  The missing values were then identified properly (from -200 to np.nan).

Next some EDA was performed in which we plotted each of the features over time as well as examining their boxplots.  Histograms with densities overlaid showed basic distribution information, and first-order lag plots saw linear relationships for the examined features (including avg. CO).  Then some of the time features were taken out of the index and put in to their own columns - hour, day of week, etc. - and the features were examined on these timescales.  In our last steps before modeling we removed the NMHC(GT) column as it was missing nearly all of it's data, and we imputed the missing values for our target feature using sklearn's KNNImputer.  Possible outlier were identified throughout this process, but a lack of domain knowledge meant that we could not reasonably remove them.  The final dataframe at this point was saved as imputedCOData.csv and cleanedData.csv (with/without the imputed CO) for future work.

After EDA was our modeling section.  We first split the data into roughly 80-20 train/test by date and using a very simple XGBRegressor created our first model using all the current features resulting in an RMSE of ~0.629.  Feature importance for this was plotted showing that the concentration of C6H6 was the most important, and interestingly, our time-derived featres of hour, day of year, etc. were among the least important.  

Since there are obviously some issues with simply splitting the data as we did, we then create another model utilizing sklearn's TimeSeriesSplit.  We also add on lag features and use only the temporal features here to compare model outcomes.  

Another model using the full dataset is created to attempt to do some forecasting using only the target feature and associated temporal data.  

At this point, no real hyperparameter tuning has been done with our models.  We've also not done a lot with the non-temporal features despite seeing their importance from our first model.  To fix this, we impute the missing values for each of our non-temporal features and then use hyperopt to tune our full model.  

This full model unfortunately uses the original model's 80-20 train/test split due to time and cost constraints.  See [Future Work](#future-work).

As an aside, this was my first time using hyperopt as I usually use GridSearchCV, but it is quite nice! The full run took quite a while - 1hr40m - to run and ended up giving a best loss of ~0.627 which was negligably better than what we got with our very first, incredibly simple model.

## Conclusions
Overall, with the data given, reasonable-looking forcasting can be done using only temporal data - including lag features.  For better model performance the inclusion of other features such as the average C6H6 concentrations should be included, but the additional performance is nearing negligable.  

Creating a TimeSeriesSplit as opposed to a  date-defined 80-20 split seemed to confer no benefits in this notebook, though it does seem like keeping the TimeSeriesSplit would be preferred as the loss of cross-validation will surely come to bite if left alone.  Unfortunately this was not possible with our full model at this time, so our final, best model uses all the features - albeit with column sampling - and performs about the same as the original basic model.  As such the model that we might choose to use right now would be our original, simple one due to the speed and simplicity.

## Future Work
Some things which I would like to add on in the future include:
- Clean up notebook, move images to data/miscImages or something to save but de-clutter.
- Combining the large search space of the XGBoost model with HYPEROPT with sklearn's TimeSeriesSplit for a more robust model.  The reason that this was not done in the first place was the time and cost of such an extensive search.  It may end up being the case that a smaller space is defined for our search before this is done.
- Creating a PowerBI or Tableau presentation.  These are both technologies that I have used but don't have much to show for it.  It could be nice to add some basic presentations with these to show competency. 

## Data Citation
https://archive.ics.uci.edu/ml/datasets/Air+Quality

S. De Vito, E. Massera, M. Piga, L. Martinotto, G. Di Francia, On field calibration of an electronic nose for benzene estimation in an urban pollution monitoring scenario, Sensors and Actuators B: Chemical, Volume 129, Issue 2, 22 February 2008, Pages 750-757, ISSN 0925-4005
