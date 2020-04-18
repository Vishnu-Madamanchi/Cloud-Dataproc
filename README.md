# Cloud-Dataproc
Building a Cloud dataproc cluster with Apache Spark 
This example is a simple demo Spark application that translates words using Google's Translation API and running on Cloud Dataproc.

Prerequesites
You are familiar with scala and have Maven and/or SBT installed. We will use scala to communicate with Spark.

You have the Cloud SDK installed.

You have a Google Cloud project and have enabled billing.

Running the code
Record the project ID in an environment variable for later use:

export PROJECT=$(gcloud info --format='value(config.project)')
Enable the translate and dataproc APIs:

gcloud services enable translate.googleapis.com dataproc.googleapis.com
Compile the JAR (this may take a few minutes):

Option 1: with Maven
cd maven
mvn package
Option 2: with SBT
cd sbt
sbt assembly
mv target/scala-2.11/translate-example-assembly-1.0.jar target/translate-example-1.0.jar
Create a bucket:

gsutil mb gs://$PROJECT-bucket
Upload words.txt to the bucket:

gsutil cp ../words.txt gs://$PROJECT-bucket
The file words.txt contains the following:

cat
dog
fish
Create a Cloud Dataproc cluster:

gcloud dataproc clusters create demo-cluster \
--zone=us-central1-a \
--scopes=cloud-platform \
--image-version=1.3
Submit the Spark job to translate the words to French:

gcloud dataproc jobs submit spark \
--cluster demo-cluster \
--jar target/translate-example-1.0.jar \
-- fr gs://$PROJECT-bucket words.txt translated-fr
Verify that the words have been translated:

gsutil cat gs://$PROJECT-bucket/translated-fr/part-*
The output is:

chat
chien
poisson
