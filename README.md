# Reasonable Suspicion - Terry Stops Prediction  
![alt text](/Assets/traffic-polic-stop.jpg "Traffic Police Stop")

## Business Understanding 
In 1968, the United States Supreme Court made a landmark decision that made it constitutional for American police to stop and frisk a person if they reasonably suspected that the said person was armed or involved in a crime. Even without probable cause for arrest, an officer does not violate the Fourth Ammendment to the constitution that prohibits unreasonable searches by stopping and frisking a person as long as they have reasonable suspicion.  

Such an investigative 'stop and frisk' stop became widely known as a Terry Stop (from the case *[Terry v Ohio](https://www.law.cornell.edu/supremecourt/text/392/1))* and the data recorded during such stops is available on [Seattle's Official City Government website.](https://data.seattle.gov/Public-Safety/Terry-Stops/28ny-9ts8)

### Business Problem
To decide on whether or not to make such a stop, an officer is required to pay attention to several factors about the sorrounding, time of day and particulars of the subject. The Seattle Police Department aims to reduce crime by ensuring they **accurately** stop, frisk and arrest as many suspicious individuals as possible. However, they need to strike a balance between stopping criminals and inconveniencing innocent citizens.

### Business Solution
This project aims to use machine learning models to help officers acheive the above goal. The dataset used contains the above factors and intends to use this historical information to help predict whether or not a Terry Stop should be made given prevailing circumstances. 

### Stakeholders
- Seattle Police Department 

## Data Understanding 
Each row in the dataset contains a unique record of a Terry Stop, as reported by the officer conducting the stop. This section will load and explore the data to understand the structure, size and observable patterns.

It will also cover the cleaning and manipulation of the data as will be deemed necessary.  

### Exploratory Data Analysis
The dataset has 57730 rows and 23 columns

Distribution of our target variable, 'Arrest Flag':

![alt text](/Assets/arrest%20flag%20output.png "Arrest Flag distribution")

The classes are imbalanced, and this should be taken into consideration during the modelling process.

History of Terry Stops over time:
![alt text](/Assets/time%20output.png "Terry stops over time")

From the plots above, we can make the following obeservations:
* Most Terry stops (90%) ended up with no arrests. **This may suggest class imbalance**
* Male subjects were stopped most, at 79%
* People with 'White' as their perceived race were stopped most, at 49%
* Officers with 'White' as race on record made the most stops, at 49%
* The most common subject age group was 26-35 years (33%)

## Data preparation and feature engineering
- None of our columns had null values, so no cleaning was necessary.
- All features were categorical so we didn't need to scale the features. 
- Used pd.get_dummies to convert categorical values to numerical
- Grouped Weapon Type column to reduce the number of extra features that were to be created 
- Converted reported date to weekday or wekend 
- Converted Reported Time column to morning, afternoon, evening or night 
- Split the data into predictor and target 
- Split the data into training and test sets 

## Modelling
Since this is a classification task, we start with logistic regression to determine whether or not a Terry stop should be made given the information contained in our predictor variables.
The results of the first model were as follows:
![alt text](/Assets/model%201%20results.png "Arrest Flag distribution")

The model has a very high false negative rate, probably due to class imbalance. In the test set, 90% of the values belonged to class '0'

**Applying business context, applying this model would lead to very many suspicious drivers not being arrested. Consequences of failing to arrest a potential criminal are more serious than if an innocent person was arrested.**

We will therefore be optimizing for recall score.
### Model 2
Weighting logistic regression due to imbalance. Results of this model were as follows:
![alt text](/Assets/model%202%20results.png "Arrest Flag distribution")

* This model predicted 1447 out of 1483 arrests correctly, which is a huge improvement!
* However, precision really took a hit and dropped to 27%. This means that we would be stopping a lot of people unncesarily who would turn out to be false negatives. The model predicts 3,834 wrong arrests.
* The test and train AUC are close, so the model generalizes well too. 

### Model 3 
While the model above would help us arrest 98% of people that do need to be arrested, it would be at the expense of a lot others who don't deserve to be arrested. Perhaps we should find a balance. We create a third model, optimizing for f1. The results turn out as follows:

![alt text](/Assets/model%203%20results.png "Arrest Flag distribution")

The only difference here was a very marginal decrease in train area under curve. For Logistic regression models, the one with invereted class weights appears to be the best.
### Model 4 
We now try a decision tree algorithm to try and get a better model for our problem.The results turn out as follows:

![alt text](/Assets/model%204%20results.png "Arrest Flag distribution")

The model performs fairly similar to the base logistic regression model. For our next model, we try tuning max_depth to see if we can get the model to perform better. We end up with 23 as the depth but a slightly worse performing model as follows:

![alt text](/Assets/model%205%20results.png "Arrest Flag distribution")

For the final model, we end up with one that predicts none of cases as arrests, so we do not consider it. 
Below is it's result:

![alt text](/Assets/model%206.png "Arrest Flag distribution")

## Conclusion:
In the end, our logistic regression model with inverted class weights ended up being the best performer given our business context. 

While it had a low precison score, the model would allow us to arrest 98% of suspects who should be rightfully taken in. However, it should be used carefully as the high rate of false negatives could have legal consequences for the officer making the Terry Stop. 