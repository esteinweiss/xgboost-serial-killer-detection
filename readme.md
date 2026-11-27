# Cold Trail: Tracking Hidden Serial Killers and the Counties That Fall Short

Emma Steinweiss

**[View the full project website](https://esteinweiss.github.io/xgboost-serial-killer-detection/)**

**Question 1: Can unsolved murders point to a hidden serial killer?** If murders of women in one area keep going unsolved when they normally wouldn't be, one killer may be behind them. I used a model to predict how often each murder *should* be solved, flagged groups of victims that fell far short, and checked whether known or suspected serial killer activity was among them.

**Question 2: Which counties solve murders worse than they should?** Some places get harder cases than others, so raw solve rates aren't fair. Using the same model, I compared each county's actual solve rate with what its cases predict, to see which places fall short.

## Data
I used the [Murder Accountability Project's](https://www.murderdata.org) version of the FBI Supplementary Homicide Reports, which covers 929,433 U.S. homicides from 1976 to 2025. I used the 793,144 murders from 1976 to 2019, since more recent cases might still get solved. Each row is one victim, with the year, state, county, victim age, sex, and race, the weapon, and the type of police agency. About 70% of the murders were solved, meaning police identified who did it. I also built two lists to test the flags: [50 serial killers who were caught](data/sk.csv) and [11 unsolved series](data/sk_unknown.csv), each with a news or court source.

## Who and Where
The serial-killer clusters show *who* is being failed. The county comparison shows *where* murders go unsolved overall. Neither one shows the full picture alone, because a county that does well overall can still hide a group of cases that went cold.

## Notebooks

1. [**Clean:**](notebooks/01_Clean.ipynb) prepare the data and remove leakage.
2. [**Model:**](notebooks/02_model.ipynb) train and tune XGBoost, and compare it to logistic regression and a decision tree.
3. [**Clusters:**](notebooks/03_Clusters.ipynb) score groups of murders.
4. [**Validate:**](notebooks/04_Validate.ipynb) test against known and suspected serial killer activity.
5. [**County Comparison:**](notebooks/05_ReportCard.ipynb) rate how well each county solves murders.


## Discovery
Unsolved murders can point to a hidden serial killer. My model was 74% accurate vs. 70% for guessing "solved" (AUC 0.748), and its predictions matched real solve rates. The flags caught 3 of the 50 serial killers I tested (Yates, Gilyard, and Colvin) and 1 of the 11 unsolved series, the West Mesa murders, which are still open. Only 2.5% of all groups get flagged, but groups with a known killer were flagged 3.8 times that often, and unsolved series 4.4 times. The method works best on cold cases, since once a killer is caught, their murders often get marked solved and stop looking unusual.

Some counties also solve far fewer murders than their cases predict. 58 of the 449 counties I compared fell clearly short, mostly big cities, and Richmond, VA was the worst at 57% solved when the model expected 71%. The same gaps show up nationally: since about 1990, solve rates have dropped more than the types of murders can explain, and murders of Black victims are solved 66% of the time, compared to 72% for the same cases with a white victim.

The two tools catch different problems. Jackson County, MO (Kansas City) solved 6.4 points more murders than expected, yet Lorenzo Gilyard's killings went unsolved for years and still got flagged. A county that does well overall can still hide a cluster, so both provide unique perspective.

## Limitations
Some murders never make it into the data. FBI reporting is voluntary, and a killer who hides bodies leaves no murders to flag. The data also only covers 1976–2019.

Once a killer is caught, their murders usually get marked solved, so many known killers can't be found this way. 16 of my 50 confirmed killers couldn't be scored at all, either because their murders were marked solved or because their group had fewer than 5 unsolved incidents. I also only looked for clusters with female victims.

The groups are built from county, victim sex, weapon, and decade, so a killer who crosses county lines or changes weapons gets split across groups and is harder to catch. I kept groups with at least 5 separate unsolved incidents, so one fire or shooting with many victims couldn't count as a cluster, and flagged scores below −3, which are very unlikely by chance. Different cutoffs would flag different groups.

My case lists are small and I built them myself. The 3.8x and 4.4x come from just 3 and 1 flagged groups, so one more or one fewer would change them a lot, but both are still well above what chance would give.

The model doesn't know neighborhood, caseload, or whether witnesses talk, so a gap shows where to look, not why it's there. The same goes for the race gap: it shows a difference, not the cause. A flag means a group is worth a closer look, not that a serial killer is behind it.

## Running the Notebooks
Run the notebooks in order, 01 through 05. The raw data (`reports.csv`) is too large for GitHub, so download it from the [Murder Accountability Project](https://www.murderdata.org) and put it in the `data` folder. Each notebook saves the files the next one needs.