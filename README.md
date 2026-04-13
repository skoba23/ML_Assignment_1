# House Prices - Advanced Regression Techniques

##  კონკურსის მიმოხილვა
House Prices კონკურსის მიზანია აღწეროს ამერიკის ერთ-ერთ შტატში საცხოვრებელი სახლების ასპექტები და განსაზღვროს მათი საბოლოო ფასი. testSet-ის თითოეული ID-ისთვის უნდა განისაზღვროს SalePrice ცვლადის მნიშვნელობა.


## რეპოზიტორიის სტრუქტურა
```
ML_Assignment_01/
│
├── model-experiment.ipynb     ← Cleaning, Feature Engineering, Feature Selection, Training, გატესტვა სხვადასხვა მოდელებზე
├── model-inference.ipynb      ← საუკეთესო მოდელის ატვირთვა Registry-ში. submission.
├── README.md                  ← რეპოზიტორიის აღწერა
```
## Data Cleaning
### სვეტებში დაკარგული მნიშვნელობების შემოწმება
შევამოწმე იყო თუ არა დაკარგული მნიშვნელობები სვეტებში. აღმოჩნდა, რომ NaN არ ყოფილა
### One-Hot Encoding
მონაცემები დავყავი ორ ჯგუფად, კატეგორიულად და რიცხვითად.
კატეგორიული სვეტები გადავიყვანე რიცხვითში.
train და test დატაფრეიმები გავასწორეთ ისე, რომ ერთნაირი რაოდენობის სვეტები ჰქონოდათ.
## Feature Engineering
დავამატეთ 3 ახალი feature.
- **TotalSF** (სახლის საერთო ფართობი): ფორმულა: *TotalBsmtSF + 1stFlrSF + 2ndFlrSF*.
გვიჩვენებს, თუ რა ფართობი აქვს სახლს სართულებისა და სარდაფების ჩათვლით. გრაფიკზე ჩანს მისი მკაფიო დადებითი კორელაცია SalePrice სვეტთან. რაც უფრო დიდია სახლის ფართობი, მით მეტია ფასიც. 0.782260 კორელაცია.
<img width="1084" height="550" alt="image" src="https://github.com/user-attachments/assets/153cc7d2-43cb-4614-830c-a73f1c141157" />
  
- **HouseAge** (სახლის ასაკი) - ფორმულა: *YrSold - YearBuilt*.
გვიჩვენებს რამდენად ახალია სახლი. გრაფიკზე ჩანს უარყოფითი კორელაცია. რაც უფრო ახალია სახლი, მით უფრო მაღალია ფასი. 0.523350 კორელაცია.
<img width="1056" height="520" alt="image" src="https://github.com/user-attachments/assets/04d796fd-142a-479f-8dee-1439cd0c9d4e" />

- **YearSinceRemodel** (რემონტიდან გასული წლები) - ფორმულა: *YrSold - YearRemodAdd*.
გვიჩვენებს რემონტიდან გასულ დროს წლებში. აქაც SalePrice სვეტთან feature-ს გააჩნია უარყოფითი კორელაცია. რაც უფრო ცოტა დროა გასული რემონტიდან, მით უფრო ძვირია სახლი. 0.509079 კორელაცია.
<img width="1033" height="522" alt="image" src="https://github.com/user-attachments/assets/29be8bc7-b7b6-40dc-89da-dbf7bd49dc08" />
შევამოწმე თითოეული სვეტის კორელაცია SalePrice-თან, რომელიც არის ჩვენი target.

## Feature Selection
კორელაციის ანალიზის გამოყენებით, ავარჩიე კორელაციის ფილტრი.
- Threshold = 0.05
მასზე ნაკლები კორელაციის სვეტები გამოუსადეგარია, ამიტომ ამოვაგდეთ.

#### 300 სვეტიდან დარჩა მხოლოდ 196 სვეტი

### როგორ შევარჩიე Threshold
- თუ Threshold-ს გავზრდიდი (მაგ. 0.5-მდე), მაშინ წაიშლებოდა ბევრი სასარგებლო სვეტი. მოხდებოდა Underfitting.
- თუ Threshold-ს შევამცირებდი (მაგ. 0.01-მდე), მაშინ მოდელში დარჩებოდა ბევრი მსგავსი ცვლადი. მოხდებოდა Overfitting.
რამდენიმე სხვადასხვა ვარიანტის მცდელობის შემდეგ შევარჩიე საუკეთესო threshold - **0.05**.

## Training
გამოვიყენე სამი განსხვავებული მოდელი: Random Forest, Linear Regression და XGBoost. ყველა ექსპერიმენტი დავარეგისტრირე Mlflow-ზე DagsHub-ში.
თითოეული მოდელისათვის გამოვიყენე მეტრიკი rmse და შემდეგი პარამეტრები:

Random Forest: 
- `n_estimators`, `random_state`

XGBoost:
- `n_estimators`, `learning_rate`, `max_depth`
- `subsample`, `colsample_bytree`, `random_state`, `min_child_weight`
სამივე მოდელზე ცალ-ცალკე გავტესტე პარამეტრები და ვნახე, თუ რომელი მოდელი მაძლევდა ყველაზე დაბალ კვადრატულ გადახრას.
### Random Forest
**RMSE = 30003.39**
| Run Name | Model Type | n_estimators | RMSE |
|---|---|---|---|
| RandomForestBaseline | RandomForest | 100 | 30003.39 |

### Linear Regression
**RMSE = 29661.61** *(წინა მოდელზე უკეთესი)*

| Run Name | Model Type | RMSE |
|---|---|---|
| LinearRegressionBaseline | LinearRegression | 29661.61 |

### XGBoost
**RMSE = 26139.49** 
აქიდან კი ჩანს, რომ სამი მოდელიდან საუკეთესო შედეგი აჩვენა XGBoost-მა.
ამიტომაც გადავწყვიტე მასზე გამეტესტა სხვადასხვა ჰიპერპარამეტრები და მენახა, რამდენად შეიცვლებოდა კვადრატული გადახრა და რამდენად გაუმჯობესდებოდა შედეგი.

| Run Name | Model Type | n_estimators | learning_rate | max_depth | subsample | colsample_bytree | min_child_weight| RMSE |
|---|---|---|---|---|---|---|---|---|
| XGBoostBaseline | XGBoost | 100 | 0.1 | 5 | _ | _ | _ | 26139.49 | 
| XGBoostUpgraded | XGBoost | 500 | 0.05 | 4 | 0.8 | 0.8 | _ | 25063.18 | 
| XGBoostUpgraded_v2 | XGBoost | 1000 | 0.01 | 4 | 0.8 | 0.8 | 3 | 25302.92 | 

შესაბამისად საუკეთესო შედეგი დააფიქსირა მოდელმა `XGBoostUpgraded`, რომელიც შეირჩა საუკეთესო მოდელად.

```
with mlflow.start_run(run_name = "XGB_Best_Model"):
    mlflow.sklearn.log_model(model_XGB_2, artifact_path="model", registered_model_name="House-Prices-XGBoost")
    mlflow.log_metric("rmse", rmse_XGB_2)
```
**შერჩევის კრიტერიუმი**: ყველაზე დაბალი RMSE, ანუ მოდელი საშუალოდ რამდენით ცდება პროგნოზში.

<img width="1326" height="124" alt="image" src="https://github.com/user-attachments/assets/f7038e89-f470-45d1-a474-651b1bd4a66b" />

## MLflow ექსპერიმენტები DagsHub-ზე
run-ები რეგისტრირებულია საიტზე: https://dagshub.com/skoba23/ML_Assignment_1.mlflow/#/experiments/0

