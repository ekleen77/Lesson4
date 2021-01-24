# How Machine Learning Can Improve Disaster Response
![](https://nplusonemag.com/wp-content/uploads/2017/09/37178284932_44dedea535_z.jpg)
<br><sub>source: https<span></span>://nplusonemag<span></span>.com/online-only/online-only/austerity-natural-disaster/</sub>

## Table of Contents
1. [Folder Structure](#Structure)
2. [Installation](#Installation)
3. [Project Motivation](#Motivation)
4. [ETL Pipeline](#ETLPipeline)
5. [ML Pipeline](#MLPipeline)
6. [Evaluation](#Evaluation)
7. [Deployment](#Deployment)
8. [Licensing, Authors, Acknowledgements](#License)

## <a name="Structure"></a>Folder Structure
-   app  
    | - template  
    | |- master.html  # main page of web app
    | |- go.html  # classification result page of web app  
    |- run.py  # Flask file that runs app
-   data  
    |- disaster_categories.csv  # data to process  
    |- disaster_messages.csv  # data to process  
    |- process_data.py  # python code to combine and clean datasets
    |- DisasterResponse.db # database to save clean data to
-   models  
    |- train_classifier.py  # python code that generates and exports the ML model
    |- classifier.pkl  # saved model

## <a name="Installation"></a>Installation
1. Additional Libraries: No additional libraries are required.

2. ETL Process: In the 'data' folder execute the command line below to process the datasets and output the sqlite database.
<br>'python data/process_data.py data/disaster_messages.csv data/disaster_categories.csv data/DisasterResponse.db'

2. ML Model: In the 'models' folder execute the command line below to use the cleaned date to generate the ML model.
<br>'python models/train_classifier.py data/DisasterResponse.db models/classifier.pkl'

3. Flask Web App: In the 'app' folder execute the command line below to initiate the web app then open in web browser.
<br>'python run.py'

## <a name="Motivation"></a>Project Motivation
Use real-world data to explore the use of Pipelines and Machine Learning models in Python classify text messages during a disaster.

## <a name="ETLPipeline"></a>ETL Pipeline
The original data is contained in 2 separate files, one containing text messages in original language and an English translation.  The second file contains all of the classifications that relate to each message.  Some of the challenges with the raw data are 1) a single message may appear multiple times with different unique id numbers, 2) the categories for each message combined into a single text string and need to be parsed, and 3) some messages have a category indicator of 2 while the only values allowed should be 0 or 1.  During this process, the data is read in from both files and joined using the \[id\] field.  The categories are then separated into individual columns and the numerical values of 0 and 1 are then used to indicate if the message is or is not associated.  Finally, the rows with duplicate messages or invalid numerical values are removed from the data.  The resulting clean dataset is then exported to a sqlite database 'DisasterResponse.db' for use by the ML Pipeline process.

## <a name="MLPipeline"></a>ML Pipeline
The ML process uses the cleaned dataset contained in 'DisasterResponse.db' to construct a ML Model to predict the categories that a message will be associated with.  First the data is read in and split into Features (message column) and Results (36 category columns).  Then a Pipeline is contructed using 2 transformers and 1 estimator.  The transformers perform Natural Language Processing (NLP) and the message data to normalize for case and punctation, separate individual words, remove commonly used words, lemmatize words, and calculate a term frequency distribution.  The results of the NLP transformation are then used by the estimator which is a Random Forest classification algorithm to develop a ML model.  The model is then trained using a portion of the original dataset called the Test set.  In order to optimize performance, different values for parameters are testing using the GridSearchCV class.  In this case I tested a range of values for min_samples_split (2, 3, 4) and n_estimators (10, 50, 100).  Of the values tested, the optimal values returned were min_samples_split=4 and n_estimators=100, however these parameters caused the processing time to be a few hours so for deployment I used min_samples_split=2 and n_estimators=1 in order to produce results in a reasonable period of time.  Given more time to work on this I would test other parameters and additional values for the ones I did test.  The final model was exported to a pickle file to be used by the Flask web app.

## <a name="Evaluation"></a>Evaluation
Model performance was assessed using classification_report to generate the f1 and recall values for each category.  I did find that the model performance improved slightly by using the Grid Search recommended values so I feel confident that, given more time, I could test additional values and parameters to improve it even more.  I also evaluated another model using the KNeighborsClassifier class as the estimator with default settings.  I found that the results of this model were significantly worse than the Random Forest model using default settings, but this may change given more time to try other values as well.

![](https://github.com/ekleen77/Lesson3/blob/main/Results.PNG?raw=true)

## <a name="Deployment"></a>Deployment
For this project, the ML model is deployed using a Flast web application.  The app allows the user to enter new messages and see what categories are predicted to be associated with the model as well as displaying key demographics of the dataset.

## <a name="License"></a>Licensing, Authors, Acknowledgements
The disaster message data was provided to Udacity by Figure Eight.
