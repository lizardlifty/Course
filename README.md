---
title: "Practical Machine Learning Course Project"
author: "Your Name"
output: html_document
---

```{r setup, include=FALSE}
library(caret)
library(rpart)
library(randomForest)
library(rpart.plot)
library(e1071)
set.seed(12345)
```

## Tujuan

Tujuan dari proyek ini adalah membangun model pembelajaran mesin untuk memprediksi variabel `classe`, yaitu cara subjek melakukan aktivitas fisik berdasarkan data dari sensor pada tubuh mereka.

## Pemrosesan Data

```{r data-loading}
training_url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testing_url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

training <- read.csv(training_url, na.strings = c("NA", "", "#DIV/0!"))
testing <- read.csv(testing_url, na.strings = c("NA", "", "#DIV/0!"))
```

### Pembersihan Data

Kita buang kolom yang memiliki banyak nilai kosong dan kolom ID.

```{r cleaning}
# Buang kolom dengan banyak NA
non_na <- sapply(training, function(x) mean(is.na(x))) < 0.95
training <- training[, non_na]
testing <- testing[, non_na]

# Buang kolom non-informatif (seperti timestamp, name)
training <- training[, -c(1:7)]
testing <- testing[, -c(1:7)]
```

## Pembagian Data

```{r partitioning}
inTrain <- createDataPartition(training$classe, p = 0.7, list = FALSE)
train_set <- training[inTrain, ]
validation_set <- training[-inTrain, ]
```

## Pemilihan dan Pelatihan Model

Kita akan mencoba beberapa model dan memilih yang terbaik berdasarkan akurasi.

### Random Forest

```{r rf-model}
rf_model <- train(classe ~ ., data = train_set, method = "rf", trControl = trainControl(method = "cv", number = 5))
rf_pred <- predict(rf_model, validation_set)
confusionMatrix(rf_pred, validation_set$classe)
```

## Evaluasi Model

Model Random Forest memberikan akurasi tinggi dan kestabilan terhadap noise serta data hilang, menjadikannya pilihan terbaik untuk prediksi ini.

## Prediksi Kasus Uji

```{r prediction}
final_prediction <- predict(rf_model, testing)
final_prediction
```

## Kesimpulan

Model Random Forest berhasil memprediksi variabel `classe` dengan akurasi tinggi. Validasi silang 5-fold digunakan untuk mencegah overfitting. Hasil prediksi terhadap 20 observasi uji telah dihasilkan dan siap dikumpulkan.

---

**Catatan:** HTML hasil kompilasi dapat dilihat pada repo GitHub melalui branch `gh-pages`.
