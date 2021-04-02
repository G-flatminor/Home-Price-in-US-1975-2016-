# HOME PRICE PRICE CHANGE IN US FROM 1975 TO 2016

## PROJECT: VISUALIZE THE US HOUSING PRICES OVER YERS (1975-2016)
+ I would like to visualize the US housing price data for each state
+ Base data was available at Kaggle.com (lists US housing market index data available to the public)
[ushouseprice](https://www.kaggle.com/PythonforSASUsers/hpindex)
+ After downloading the data, it was cleaned and processed for visualization


## Data examination and cleaning
+ Ran the following code to take a base look at the dataset.

      HPI = pd.read_csv('HPI_Master copy.csv')
      print(HPI.head())
      print(HPI.dtypes)

+ The code shows result as follows:

      hpi_type     hpi_flavor frequency                   level  \
      traditional  purchase-only   monthly  USA or Census Division   
      traditional  purchase-only   monthly  USA or Census Division   
      traditional  purchase-only   monthly  USA or Census Division   
      traditional  purchase-only   monthly  USA or Census Division   
      traditional  purchase-only   monthly  USA or Census Division   

      place_name place_id    yr  period  index_nsa  index_sa  
      East North Central Division   DV_ENC  1991       1     100.00    100.00  
      East North Central Division   DV_ENC  1991       2     101.03    101.11  
      East North Central Division   DV_ENC  1991       3     101.40    101.08  
      East North Central Division   DV_ENC  1991       4     101.79    101.10  
      East North Central Division   DV_ENC  1991       5     102.44    101.50 

      hpi_type       object
      hpi_flavor     object
      frequency      object
      level          object
      place_name     object
      place_id       object
      yr              int64
      period          int64
      index_nsa     float64
      index_sa      float64
      dtype: object

## Data (10 variables)
    1. HPI type (categorical: developmental, traditional, non-metro, distress-free)
    2. HPI flavor (categorical: all-transactions, expanded-area, purchase-only)
    3. Freequency (categorical: monthly, quarterly)
    4. Level (categorical: MSA, Puerto Rico, State, USA or Census Division)
    5. Place name (categorical formatted in "major city name, State" such as Akron, OH, and Asheville, NC)
    6. Place ID (categorical: zip code or state abs)
    7. Year (numerical)
    8. Period (ordering variable for month)
    9. Index nsa (non-seasonally adjusted price, numerical)
    10. Index sa (seasonally adjusted price, numerical)

 ## Data cleaning
   We need only the following 3 items:
      + State
      + Year
      + Price (annual average)


    







