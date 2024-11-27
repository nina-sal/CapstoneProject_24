# Model Card

## Model Description

This model predicts the outcome of individual baseball pitches from the 2024 Major League Baseball (MLB) season. The model uses statistics from Statcast, MLB's official stats provider, to predict the outcome of a pitch being a ball, strike, or hit. The model uses Principal Component Analysis (PCA) and Extreme Gradient Boosting (XGBoost).

### Inputs:
The model uses a total of 83 features, split as follows:
- **43 pitch-level features** capturing: pitch velocity, spin rates, speed, movement, release position, where the ball crosses the strike zone, pitcher handedness (left or right), and batter stance (left or right).
- **40 features** representing seasonal averages for pitchers (20) and batters (20). The number of variables has been reduced using Principal Component Analysis (PCA). For batters, there are 5 Principal Components (PCs), and for pitchers, there are 4 PCs.

### Output:
The model outputs the probability of the outcome of a pitch belonging to one of five categories:
- **Ball**
- **Called strike**
- **Swinging strike**
- **Weak contact**
- **Solid contact**

Contact is a combination of launch angle and exit velocity as measured by Statcast. Weak contact is when a ball is either topped or hit under. Solid contact occurs when a ball is a 'flare', 'burner', or hits the barrel of the bat (the "sweet spot"). For more details, refer to MLB's [glossary](https://www.mlb.com/glossary/statcast/sweet-spot).

Outcomes such as intentional walks and foul balls were removed prior to analysis.

### Class Balancing:
The outcome classes were balanced by undersampling balls, called strikes, swinging strikes, and weak contact. This may have introduced some bias, as some variation was removed. Tables 1 and 2 outline the sample sizes before and after balancing.

#### Table 1: Sample size across outcome groups before balancing
| Class           | Count   | Proportion |
|-----------------|---------|------------|
| Ball            | 245,611 | 44%        |
| Called strike   | 112,993 | 20%        |
| Swinging strike | 76,587  | 14%        |
| Weak contact    | 74,059  | 13%        |
| Solid contact   | 46,344  | 8%         |
| **Total**       | 555,594 | 100%       |

#### Table 2: Sample size across outcome groups after balancing
| Class           | Count   | Proportion |
|-----------------|---------|------------|
| Ball            | 46,344  | 20%        |
| Called strike   | 46,344  | 20%        |
| Swinging strike | 46,344  | 20%        |
| Weak contact    | 46,344  | 20%        |
| Solid contact   | 46,344  | 20%        |
| **Total**       | 231,720 | 42%        |

### Data Split:
- **Training:** 60%
- **Validation:** 20%
- **Test set:** 20%

The final results are based on the model’s performance on the test set.

## Model Architecture

The model consists of three components:
1. **PCA of Seasonal Batting Statistics:** 
   - Reduces the number of dimensions for batting data. 
   - 20 features were reduced to 5 Principal Components (PCs), making up 80% of the variation.
   
2. **PCA of Seasonal Pitching Statistics:** 
   - Similar to batting PCA, but uses pitcher data.
   - 4 PCs were chosen, making up 64% of the variation.

3. **XGBoost Model:** 
   - eXtreme Gradient Boosting (XGBoost) uses ensemble learning to combine predictions from multiple weak learners.
   - The model optimizes a differentiable loss function (multi-class logarithmic loss) using gradient descent.
   - Hyperparameters were optimized using Optuna.

## Performance

### Metrics:
Models were validated using the following metrics:  
- **Accuracy**: Overall percentage of correct classifications.
- **Sensitivity (Recall)**: For each class, the proportion of correct classifications.

The final model showed an overall accuracy of 55%,  with the following sensitivity rates across classes: 

#### Table 3: Sensitivity (recall) rates of each predicted class
| Class           | Recall |
|-----------------|--------|
| Ball            | 78%    |
| Called strike   | 69%    |
| Swinging strike | 50%    |
| Weak contact    | 32%    |
| Solid contact   | 46%    |

 The following plots illustrate the model's performance:

#### Figure 1: Confusion matrix of final model
(Confusion matrix showing the count of predicted vs actual classes.)

#### Figure 2: Violin plots of prediction probability by predicted class
(Shows distribution of probabilities for each predicted class.)

### Performance Summary:

Both the sensitivity rates and probability distributions show that the model was more successful at classifying balls and performed the worst for weak contact. Both weak and solid contact have a high density of prediction probability at around 40%, suggesting the model has less certainty when predicting these outcomes.  

Given this model is designed to predict a sporting outcome, a very high accuracy was not expected.  However there are a few findings of particular note:  the model is able to detect, at a 70% success rate, that a ball in the strike zone will be a called strike and therefore not swung at. This is an important finding when making management decisions especially for a tight game when getting a strike would be the most desired outcome. 

Further, although solid contact only has a 46% success rate, it’s being misclassified as weak contact 25% of the time. Combined, this makes over 70% (not including accurate predictions for a weak contact). Again, in a tight game a manager would want to get any type of hit over a strike out.  

### Limitations and Biases

#### Limitations:
- The model only uses data from the 2024 MLB season, it may therefore not be representative of earlier or future season which may lower predictive accuracy. 

- The model only predicts the outcome of a standalone pitch, it does not take into account pitch sequence and therefore does not predict the outcome of a plate appearance.   This may be an important aspect of predicting the outcome of a pitch as the sequential nature of a plate appearance may be an important factor.  

- The model also doesn’t take into account external factors such as the ballpark, home plate umpire, or weather, all of which can affect pitcher performance and outcomes. 

#### Biases:
- Seasonal data is used to capture broad information about a batter and pitcher’s overall performance. This approach introduces bias into the data as the outcome of each individual pitch contributes to the seasonal averages which may influence the predictions. Ideally a cumulative average prior to a given pitch or previous season averages should be used however this presented data challenges such as a small sample size, people that didn't pitch/bat in the previous season etc. For simplicity this approach was chosen however people with a low numbers of plate appearances / batter faced was removed so that an individual pitch would have a negligible influence on the seasonal average. 

- By balancing the outcome classes, much of the data had to be removed (around 80% for balls, 60% for called strikes and 40% for swinging strikes and weak contact). Without this balancing most of the models tested would fail to predict the low frequency classes. This approach may be introduced some bias into the data and valuable information may have been removed.  

### Trade-offs:
the model is better at making predictions for balls and called strikes, and has worst performance for weak and solid contact. 
For weak contact specifically, it is slightly more likely to be classed as strong contact (34% compared with 32%) which should be borne
in mind when using the model.  

## Future Research

This model is a prototpye to establish the viability of predicting pitch outcomes. Future iterations of the model would seek to address some of the limitations outlined in the previous section such as expanding the number of baseball seasons and capturing the sequential aspects of a plate appearance. 
