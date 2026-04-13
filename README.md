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
- **TotalSF** (სახლის საერთო ფართობი): გვიჩვენებს, თუ რა ფართობი აქვს სახლს სართულებისა და სარდაფების ჩათვლით. გრაფიკზე ჩანს მისი მკაფიო დადებითი კორელაცია SalePrice სვეტთან. რაც უფრო დიდია სახლის ფართობი, მით მეტია ფასიც.
<img width="512" height="254" alt="image" src="https://github.com/user-attachments/assets/831b5aab-0a15-421a-8438-1dd2aaaada28" />
  
- **HouseAge** (სახლის ასაკი) - გვიჩვენებს რამდენად ახალია სახლი. გრაფიკზე ჩანს უარყოფითი კორელაცია. რაც უფრო ახალია სახლი, მით უფრო მაღალია ფასი.
<img width="1056" height="520" alt="image" src="https://github.com/user-attachments/assets/04d796fd-142a-479f-8dee-1439cd0c9d4e" />

- **YearSinceRemodel** (რემონტიდან გასული წლები) - გვიჩვენებს რემონტიდან გასულ დროს წლებში. აქაც SalePrice სვეტთან feature-ს გააჩნია უარყოფითი კორელაცია. რაც უფრო ცოტა დროა გასული რემონტიდან, მით უფრო ძვირია სახლი.
<img width="1033" height="522" alt="image" src="https://github.com/user-attachments/assets/29be8bc7-b7b6-40dc-89da-dbf7bd49dc08" />

