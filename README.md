### Project carsales

### Overview
From a business perspective, we are tasked with identifying key drivers for used car prices. In the CRISP-DM overview, we are asked to convert this business framing to a data problem definition.

### Data:
In this application, you will explore a dataset from kaggle. The original dataset contained information on 3 million used cars. The provided dataset contains information on 426K cars to ensure speed of processing. Your goal is to understand what factors make a car more or less expensive. As a result of your analysis, you should provide clear recommendations to your client -- a used car dealership -- as to what consumers value in a used car.

### Summary of findings
- [Business Understanding](#business-understanding)
- [Data Understanding](#data-understand)
- [Data Preparation](#data-preparation)
- [Modeling](#modeling)
- [Evaluation](#evaluation)
- [Deployment](#deployment)
- [Next Steps](#next-steps)

### Business Understanding
There are a great number of cars in the dataset with different features. This features affect directly the price
of a car, those could increase or decrease the price.

The goal is to determine what are the features that increase the price of the car to maximise profit, for instance having a 4df, red, 2020 could have a better price that a car fwd, whit, 2020.

We can get to the point is the weight for each feature in the car price.
  
### Data Understanding
-The dataset has 426K cars and most of the features are categorical
- There are many null values in those categorical features
- The categories are not binary
- There are duplicate cars
- There are outliers prices

###  Data Preparation
1. Remove rows where manufacturer, model, condition, cylinders, fuel, odometer, title_status, transmission, drive, size, type, and paint_color and nan
2. VIN Number has duplicates (this number suppose to be unique) and many of them are null. 
This information is not useful for the model. Deleting column.
3. Cylinders for electric cars do no apply. As of March 2024, 43.4% of cars on the road in the United States have 4-cylinder engines. However, this dataset has the most frequent cylinders  as 6, so nan will be fill with 6
4. Opten for some key features such as color to fill the most frequent value
5. Leave the drive with the nan in case became a key feature I will review this again
6. Remove duplicates
7. Removing outliners there some cases price close to a billion such as 99999999 and 987654321 for a 1960 ford
8. After cleaning the data the dataset has 333445 rows and 16 columns

### Modeling
The goal of the project is to identify what car features increases the price of used cards. Because of this 
I worked with 3 model:
1. Ridge
2. SequentialFeatureSelector with Lasso
3. RFE with Lasso

Used 2 types of encoding: The two types of encoding due to memory issues with OneHotEncoder for the whole dataset
1. LabelEncoder for the whole data set (after cleaning)
2. OneHotEncoder for key features

So I repeated the 3 model for each type of encoding. In the first pass I was able to get the most important features. In the second pass the models gave details of what are most prevalent in a great detail.

### Evaluation
**First Pass**
Once the cardinal features were transformed, got the correlation matrix to show the relationship of the features and price.
As shown in the headmap graph the transmission, cylinders, and year are features with better correlation.

1. Used simple CV for all models
2. Used LinearRegression as base model
3. Created a pipeline with StandardScaler(with_mean=False) and Ridge() as regression model
4. Trying different numbers for the ridge alpha - settled for param_dict = {'ridge__alpha': np.linspace(1, 100, 100)}
5. Next model SequentialFeatureSelector with estimator = Lasso()
6. Last model RFE with estimator = Lasso()

Confirming with the coefs from the model, the permutation_importance showed results affirming the results for year(as expected), then transmission, cylinders, odometer (also expected), and fuel to be the features that add value to the car

**Second Pass**
Reduced the number of cardinal features to:  transmission, cylinders, condition, drive, fuel and transform the data using OneHotEncoder to get detailed information on the car features. 
**This time got a reduce dataset because of memory problems with the OneHotEncoder**

1. Calling fit_trasform and transform to avoid memory issues with OneHotEncoder
 the result matrix is compressed sparse
2. Decompressing the matrices calling toarray() . This gets the simple CV split
3. Calling Ridge model - Data transformed by OneHotEncoder using GridSearchCV
settled for param_dict = {'ridge__alpha': np.linspace(1, 100, 100)}
4. Running SequentialFeatureSelector with estimator as Lasso and Ridge as regression model**
Tried different parameters for the features - Settled on 6
5.  Running RFE with estimator as Lasso and Ridge as regression model**
Tried different parameters for the features - Settled on 6

Car Price drivers:
- The coefs for transmission other has a high value affecting the car price in a positve way. In the same way we have 8 cylinders, diesel, electric, 4wd, condition like new, condition new and other with smaller coefs.
- In the other hand manual and automatic transmission, condition fair, condition salvage, drive fwd, gas, hybrid, cylinders 4,5,6,3 reduce the price of the used car

### Deployment

Because the business goal in not to predict prices, but analyze what are the car features that increase the value of the car:

- After that transmission is the one of the featues that customers value more
- Next is cylinders
- and the condition. This is a bit of surprise this come to be the 4 feature customers value on the use car
In the other hand there could be some features that could reduce more the car price:
- odometer is an expected one, the higher the number in the odometer the car loses value
- the next on the category is fuel, this is also expected as price values going to the roof

Looking at these features in detail the drivers to increase the case value are:

- Price increase for transmission other helps to increase the car price, this is because continuously variable transmission (CVT) and dual-clutch transmission (DCT) are more simpler and efficient that normal automatic or manual trainsmissions
- On the car cylinders 8 cylinders gives more value to the car
- Diesel is a preferable fuel. In general diesel is more efficient.
- Next electric cars
- In terms of drive the 4wd gives helps to increase the car price
- Finally, condition "like new" or "good" are preferable on car value


### Next Steps
- Run the project with K-means clustering and compare the results
- Use PCA to reduce the number of columns after OneHotEncoder
- With shorter number of rows and columns run a polynomial model and compare results
- Use a different technique to deal with null values
- Run the modeling using region to identify features, i.e montain regions may required a 4wd more that people in the cities
  
