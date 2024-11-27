# Datasheet

This datasheet summarizes the baseball data used in the model, how it can be obtained, and what preprocessing has been done to it.

---

## Motivation

The purpose of the dataset is to track and collect statistics from Major League Baseball (MLB) in the USA. The data is created by Statcast, MLB’s tracking technology that collects a wide range of baseball metrics.

---

## Composition

### Pitch-level Statistics
- The dataset comprises all pitches from the 2024 MLB season.
- Metrics include pitch velocity, acceleration, position, etc. For further details of all variables included, refer to Statcast’s glossary: [Statcast Glossary](https://baseballsavant.mlb.com/csv-docs).
- Before cleaning: 721,611 instances.
- After cleaning: 555,594 instances (see the cleaning section for details).
- Less than 0.5% of the data had missing values (not including deprecated statistics). These rows were removed.

### Individual-level Seasonal Statistics
- Instances comprise seasonal averages for batters and pitchers for the 2024 MLB season.
- Data was downloaded directly from Statcast and includes metrics most relevant to pitching/batting performance.
- Only players with at least 50 plate appearances were included.
  - 525 batters.
  - 632 pitchers.
- No missing data.

---

## Collection Process
Data is collected by Statcast, MLB's official statistics provided. 

- **Pitch-level data**: obtained using the `pybaseball` package.
- **Batter- and pitcher-level data**: Downloaded directly from [Baseball Savant](https://baseballsavant.mlb.com/) leaderboard selecting the following custom metrics:
  - `k_percent` - strikeout percentage.
  - `bb_percent` - walk percentage.
  - `xba` - expected batting average.
  - `xslg` - expected slugging percentage.
  - `xwoba` - expected weighted On-Base Average.
  - `xwobacon` - expected weighted On-Base Average on Contact.
  - `hard_hit_percent` - percentage of batted balls with an exit velocity of 95 mph or more.
  - `sweet_spot_percent` - percentage of batted balls with a launch angle between 8 and 32 degrees.
  - `avg_hyper_speed` - average exit velocity.
  - `z_swing_percent` - percentage of in-zone swings.
  - `z_swing_miss_percent` - percentage of in-zone swings and misses.
  - `iz_contact_percent` - percentage of in-zone contact.
  - `oz_swing_percent` - percentage of out-of-zone swings.
  - `oz_swing_miss_percent` - percentage of out-of-zone swings and misses.
  - `oz_contact_percent` - percentage of out-of-zone contact.
  - `barrel_batted_rate` - percentage of batted balls with the perfect combination of exit velocity and launch angle.
  - `poorlyweak_percent` - percentage of balls hit with weak contact.
  - `poorlyunder_percent` - percentage of balls hit under.
  - `poorlytopped_percent` - percentage of balls topped.
  - `solidcontact_percent` - percentage of balls hit with solid contact.
  - `flareburner_percent` - percentage of flare/burners hit.
  - `xhr` - expected home runs (downloaded separately and merged).

---

## Preprocessing, Cleaning, and Labelling

### Removed Variables
- Deprecated statistics no longer in use.
- Fielding-related statistics or outcomes of pitches (excluding the derived outcome variable).

### Derived Variables
1. **Outcome Variable**
   - The model predicts 5 classes which represent the outcome of a pitch:
     - Ball.
     - Called strike.
     - Swinging strike.
     - Weak contact.
     - Solid contact.
   - The outcome variable has been derived from:
     - `type` (categorizing outcomes into ball, strike, or in play).
     - `description` (splitting strikes into swinging or called strikes).
     - `launch_speed_angle` (categorizing quality of contact into weak or solid contact).

     | Contact Category | Group      |
     |------------------|------------|
     | Weak             | Weak       |
     | Topped           | Weak       |
     | Under            | Weak       |
     | Flare/Burner     | Solid      |
     | Solid Contact    | Solid      |
     | Barrel           | Solid      |

2. **Other Derived Variables**
   - `on_base`: A continuous variable (0 to 3) capturing how many players are on base at the time of the pitch, derived from `on_3b`, `on_2b`, and `on_1b`.
   - `run_diff`: A continuous variable capturing the run differential between the pitching and batting team. A positive value indicates the fielding team is winning, while a negative value indicates they are losing. Derived from `fld_score` and `bat_score`.

### Removing Pitches (rows)
- Pitches for individuals with fewer than 50 plate appearances were removed.
- Pitches resulting in intentional walks or foul balls were removed.

---

## Uses
The data can be used for various pitch-level analyses, though users should note that some pitches from 2024 (e.g., foul balls) are excluded.

---

## Distribution
- The data is freely available for download and use.
- Raw seasonal statistics are available on [Baseball Savant](https://baseballsavant.mlb.com/).
- Raw pitch is available from the  `pybaseball` package. Code for downloading and cleaning is provided.


---

## Maintenance
- The data is maintained by Statcast
- It is downloaded using the `pybaseball` package (version 2.2.7, released Sep 8, 2023). There may be inconsistencies between the scraping tool and the data on Baseball Savant.
