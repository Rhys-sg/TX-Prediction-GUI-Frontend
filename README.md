# TX Prediction GUI Frontend

## Description

This interface allows undergraduate students to simulate ligated promoters in E. coli upstream of an RFP gene. After modifying a "Default" promoter by introducing base-pair mutations, the GUI presents 1) the predicted relative flourescence of the colonies using a CNN machine learning model and 2) the observed relative flourescence stored in a PostreSQL database. After submitting, the GUI inserts the modified promoter into another table in the database, which instructors can querry/download as a .csv to order the oligos.

A GUI component visualizes the process for ligating a new promoter between the GFP and RFP proteins by "cutting" the old promoter and ligating using Golden Gate Assembly.

The website also includes an interface allowing users to add observed relative flourescence entries into the database and an account system allowing regestered schools to access the GUI and track student simulated ligations/observations.


## Links

Frontend hosted at https://ecoli-promoter-prediction.onrender.com/

Backend hosted at https://ecoli-promoter-prediction-backend.onrender.com

Frontend repository https://github.com/Rhys-sg/TX-Prediction-GUI-Frontend

Backend repository https://github.com/Rhys-sg/TX-Prediction-GUI-Backend