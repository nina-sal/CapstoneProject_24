# Non-technical summary 

## Project overview 

The purpose of this project is to predict the outcome of individual baseball pitches from the 2024 Major League Baseball (MLB) season.   

The end use of this model would be to support strategic decisions during baseball games. Predicting player match-up is an important aspect of baseball decisions. Knowing that a given hitter hits well or poorly against a given pitcher is important for a manager to decide if he’s going to play that player in the game. Much of these decisions are based on historic performance (e.g. how has this batter played against this pitcher before?) however there are often situations where either the sample size is very low, or the pitcher and hitter have never met. A model to predict the likely outcome between a pitcher and batter meeting for the first time would be a valuable tool for a baseball management team.  

The types of questions this model could answer are:  

1. What is the probability that a specific pitch type from a pitcher results in a strike against a hitter they’ve never faced? 

2. How likely is a new hitter to put certain pitches in play based on their past performance against similar pitchers? 

3. Does a specific pitcher’s style (e.g., high fastballs or breaking balls) align well or poorly with a given hitter's tendencies, even if the matchup hasn’t occurred?  

## How does the model work? 

The model uses statistics from individual pitches from the 2024 season such as pitch velocity, pitch acceleration, release position, etc. together with pitcher and batter seasonal statistics. It predicts whether the likely result of that pitch will be one of five outcomes:  

- Ball – A ball is when the pitch is outside of the strike zone 
- Called strike – a pitch that is within the strike zone that the batter has swung at and missed 
- Swinging strike  - a pitch that can be either inside or outside the strike zone that the batter has swung at and missed 
- Weak contact – contact quality is measured as a combination of launch angle and speed. Weak contact in this model is a combination of either weak, topped or under as defined by statcast (MLB’s official statistics provider) 
- Solid contact - Solid contact in this model is a combination of flare/burner, solid contact, or barrel as defined by statcast (MLB’s official statistics provider) 

## How successful is the model at predicting outcomes? 

The final model is able to predict pitch outcomes at a 55% success rate. The model is more successful at predicting certain outcomes which is set out in the table below.  

| Outcome           | Success Rate |
|-------------------|--------------|
| Ball              | 78%          |
| Called strike     | 69%          |
| Swinging strike   | 50%          |
| Weak contact      | 32%          |
| Solid contact     | 46%          |


The table shows that for all pitches that are Balls, the model will correctly predict a ball 78% of the time. The poorest performance is for weak contact: for all the pitches that were hit weakly, the model will correctly predict this 32% of the time.  


## Limitations  

There are several limitations of the model including:  

- The model only looks at the 2024 season therefore it is unknown how the model would perform for past or future data. 

- The model only looks at individual pitches, and not the entire plate appearance (i.e. pitch sequence). There could be important aspects of the pitch sequence which influence the outcome that have not been considered in this project. 

- The data does not include external factors such as ball park, weather, etc. which may affect a batter or pitcher performance. 

 

# Technical details 

The model uses two machine learning methods: principal component analysis PCA) and XGBoost.  PCA is used to reduce the dimensions of the seasonal batting and pitching statistics. XGBoost is the method used to predict pitch outcomes.  

For futher technical details on the data and the model, please review the data sheet and model card.  This includes further details on the limitations and potential biases of the model.  

## What is included in this repo? 

### Data 

The github repo includes the following data: 

- Pitching statistics with PCA (pitchstats24_withPCA.csv)– This data includes seasonal statistics and the output from the PCA analysis. The raw data has not been provided but can be downloaded from https://baseballsavant.mlb.com/leaderboard/statcast. The data underwent minimal processing which is all detailed in the respective jupyter notebook.  

- Batting statistics with PCA (batstats24_withPCA.csv) - as above. 

Pitch by pitch data is not included in this repo as the file size is too large. The 'importing_cleaning' notebook contains all code to download and clean it.

 

### Jupyter notebooks 

All code is documented in Jupyter notebooks. There are four notebooks in total:  

- Batting PCA – This notebook contains descriptive statistics for seasonal batting averages, and the outputs of a principal component analysis to reduce the number of dimensions  

- Pitcher PCA - This notebook contains descriptive statistics for seasonal pitching averages, and the outputs of a principal component analysis to reduce the number of dimensions 

- Importing and cleaning  -this notebook cleans and preprocesses all of the pitch level data, and merges in data from batting and pitcher PCAs. 

- Modelling – this notebook includes descriptive statistics for the pitch level data, preparing data for modelling, and testing of four different models. The notebook also includes the outputs and performance of the final model.  

#### How to use them  

The notebooks are designed to be run sequentially in the above order. Data outputs for Batting PCA and Pitching PCA are included in this repo and do not need to be re-run. The importing and cleaning notebook includes code to download data and clean it to enable use in the modelling notebook. 

## Future research

The model at this stage is a prototype only looking at the 2024 season and high level outcomes: ball, called strike, swinging strike, weak contact and solid contact. The next steps for this model would be to incorporate additional seasons and model more granular outcomes.  

 

 
