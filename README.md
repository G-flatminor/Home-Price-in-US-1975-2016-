# HOME PRICE PRICE CHANGE IN US FROM 1975 TO 2016

## PROJECT: VISUALIZE THE US HOUSING PRICES OVER YEARS (1975-2016)
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

### Data (10 variables in original dataframe)
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

      home_prices = HPI['index_nsa']
      years = HPI.yr.unique()
      years =sorted(years)
      print(years)
        
   [1975, 1976, 1977, 1978, 1979, 1980, 1981, 1982, 1983, 1984, 1985, 1986, 1987, 1988, 1989, 1990, 1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998, 1999, 2000,                  
   2001, 2002, 2003, 2004, 2005, 2006, 2007, 2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016]


This table contains the data between 1975 and 2016.

We will create a new dataframe with the annual home price for each state by year.


      price_list = []
      for i in years:
            yearly_ave = HPI.index_nsa[HPI.yr == i].mean()
            price_list.append(yearly_ave)
      price_list = np.array(price_list)       
      level_unique = HPI.level.unique()
      states = HPI.place_name[HPI.level == 'State'].unique()
      states = np.array(states)
      
      houseprice_ave = []
      year_of_ave = []
      state_name = []
      for i in sorted(HPI.place_id.unique()):
            for j in sorted(HPI.yr.unique()):
                  ave_price = HPI.index_nsa[HPI.level =='State'][HPI.place_id == i][HPI.yr ==j].mean()
                  houseprice_ave.append(ave_price)
                  year_of_ave.append(j)
                  state_name.append(i)
      
      df = pd.DataFrame([houseprice_ave, year_of_ave,state_name]).T
      df.columns = ['price', 'year','state']
      df.to_csv('House Price List.csv')
   
## New data frame (State, Year, Annual price)

The new dataframe was created.
Currently, the new frame has homeprice for each state by month/year. So we need to calculate average home price.

      df = pd.read_csv('House Price List.csv')
      df['price'].replace('', np.nan, inplace = True)
      df.dropna(subset = ['price'], inplace = True)
      table = df.pivot(index = 'state', columns = 'year', values = 'price')
      print(table)

The result is as follows:

      year      1975     1976     1977     1978      1979      1980      1981  \
      state                                                                     
      AK     64.5450  71.1825  77.8075  84.3500   94.5375   85.9975  110.5275   
      AL     72.0375  77.0875  85.2775  92.7550  102.3950  104.9550  108.5450   
      AR     63.7325  70.0175  77.3000  84.0350   97.4050  104.1475  107.3900   
      AZ     58.5850  60.8150  65.6775  76.9475   93.9325  103.6275  112.1950   
      CA     43.6250  51.7575  64.8750  77.3150   90.7000  104.4850  114.5575   
      CO     54.4600  58.9700  68.1275  82.2075   96.0600  103.9975  111.2425   
      CT     62.2100  66.0275  69.3100  82.3375   99.1525  102.8975  110.5700   
      DC     48.5525  54.8500  65.2400  76.4775   91.9175   99.1725  100.2275   
      DE     89.6475  83.3175  81.6875  85.7375   91.2475  106.5525  109.4275   
      FL     71.6775  71.4350  74.8900  82.9125   93.5775  103.7750  111.7575   
      GA     73.5250  75.6750  77.4875  84.2825   91.5900  101.2200  109.0000   
      HI     55.3925  55.1850  59.6200  67.5900   84.1250  101.9900   83.9300   
      IA     63.0325  70.6325  79.0825  90.3175   99.6250   99.7925   94.1850   
      ID     63.8925  67.2150  76.5550  88.8375   95.9850  100.6875  106.4850   
      IL     65.0650  71.1600  83.9650  97.4200   99.7925  100.4350  105.6725   
      IN     63.0650  66.1350  73.6800  85.8800   96.8950  100.7400  105.1875   
      KS     63.1525  68.5500  76.8350  86.8100   95.5150  102.2300  102.9500   
      KY     66.5925  69.5775  74.9950  86.6975   97.5275  102.0050  105.4650   
      LA     53.7925  59.3225  64.6500  76.6150   93.1650  100.7875  109.6200   
      MA     67.7000  69.1200  72.3975  82.5225   95.6775  105.5625  119.1300   
      MD     63.9950  69.7325  74.8150  84.8475   95.1675  103.9100  112.2325   
      ME     70.9900  70.5325  82.3550  90.3350  103.7825  104.3400  116.6475   
      MI     61.8950  64.1100  69.6400  81.0575   96.4700  101.5700  104.4450   
      MN     56.8175  63.0775  70.7050  82.9875   95.4650  103.3450  103.2600   
      MO     67.1925  70.7475  77.2650  89.1725   99.2950  105.2750  103.0375   
      MS     72.3150  74.6525  79.1100  89.4075   99.9225  101.1800  102.7725   
      MT     56.2100  62.6800  72.7075  88.7375   97.9950   99.4875  101.4650   
      NC     68.2725  70.3725  76.4925  85.2600   94.0700  100.7550  105.7975   
      ND     64.8775  72.1400  81.0100  86.3200   99.4075  100.2450  107.6775   
      NE     63.6400  68.6725  76.6400  84.4000   95.6975  102.5325  101.7200   
      NH     69.8450  67.3475  71.4475  82.3500   97.1675  102.0575  105.0500   
      NJ     61.9475  63.5025  66.7975  74.8850   89.4125  101.4350  106.1375   
      NM     57.9025  62.7975  72.5400  80.3025   93.1075  100.7025  112.0575   
      NV     52.1950  58.8525  68.3100  82.6275   97.5650  103.0150  113.6425   
      NY     78.1200  74.6875  82.0975  85.0200   96.3300  108.4075  113.3000   
      OH     64.8725  69.5425  76.9100  87.2600   98.2975  101.1250  102.7475   
      OK     52.5950  58.9975  66.0750  79.0300   91.8450  100.9400  108.3975   
      OR     52.4300  59.4300  72.1100  86.2525   97.7075  101.5625   95.7150   
      PA     71.3325  75.6450  79.8675  86.2725   95.6250   99.6575  102.6100   
      RI     64.9625  68.8150  73.6525  83.5000   94.8925  101.4100  102.2700   
      SC     71.4800  74.7450  79.7275  86.5725   96.6200  104.4150  114.3975   
      SD     67.5900  67.4825  73.0875  82.7300   93.0800  101.8300   95.8675   
      TN     67.8250  69.4925  76.8475  85.7175   93.6925   99.7000  104.0825   
      TX     57.5675  62.3750  70.0775  80.7125   93.0300  103.7250  113.2025   
      UT     56.9375  62.9025  72.2800  85.4600   97.3525  101.2525  108.9500   
      VA     67.8725  69.8825  76.0275  84.7375   92.8225  102.1825  106.5325   
      VT     72.0450  72.5400  78.8525  91.1650   95.2450  103.8925   95.2700   
      WA     47.5850  54.3575  65.0775  81.1000   94.0925  101.4600  104.2800   
      WI     64.5925  67.0375  77.0325  88.9500   98.8875  102.0150  100.2750   
      WV     63.7175  67.3550  77.8250  85.2975   90.8250  106.0950  100.6175   
      WY     50.5675  58.1325  67.0250  78.1550   93.6200   98.4400  108.5375   

      year       1982      1983      1984  ...        2007        2008        2009  \
      state                                ...                                       
      AK     135.6850  141.5350  150.0675  ...  230.418125  228.780000  227.127500   
      AL     109.9525  115.0825  122.1500  ...  217.973750  216.941875  211.543125   
      AR     107.4475  116.5000  119.8250  ...  202.333125  199.758750  196.601875   
      AZ     109.8000  117.5250  121.6575  ...  316.408750  266.415625  220.523125   
      CA     116.4450  117.8000  121.2925  ...  345.939375  274.758750  237.235625   
      CO     122.6175  125.6200  126.0450  ...  284.940625  277.718125  272.194375   
      CT     112.2250  122.6725  137.5575  ...  269.838750  255.811250  240.200625   
      DC      96.7275   98.4675  104.6875  ...  423.137500  398.228333  380.303333   
      DE     112.9225  122.0150  130.3450  ...  312.947500  298.188333  281.356667   
      FL     121.1750  124.7350  127.3450  ...  333.073750  273.215625  231.074375   
      GA     114.7650  119.4000  126.1500  ...  228.731875  219.913750  207.012500   
      HI     100.5100  106.2000  110.1925  ...  308.536875  290.763750  264.487500   
      IA      93.4200   98.3450   99.5600  ...  206.795000  206.944375  206.096250   
      ID     104.2425  106.4750  109.0100  ...  267.107500  258.135000  238.375000   
      IL     102.0250  105.0575  110.5400  ...  236.078125  224.745000  210.301875   
      IN     104.1400  106.1900  107.5050  ...  188.766875  185.078125  181.100000   
      KS     104.9275  107.6025  108.1900  ...  200.299375  199.977500  199.331250   
      KY     106.6900  109.8425  112.4500  ...  209.731250  208.829375  206.487500   
      LA     114.8700  116.3500  115.8900  ...  222.673750  222.692500  221.271875   
      MA     126.3225  140.1300  169.9500  ...  359.936250  339.674375  327.777500   
      MD     113.4325  116.0400  122.6600  ...  327.644375  300.911875  273.971250   
      ME     117.2100  123.8300  139.2100  ...  284.995000  278.864375  268.450625   
      MI      97.6725   96.5575   97.3225  ...  211.068125  190.915000  176.048125   
      MN     107.9025  112.5450  114.7900  ...  267.134375  250.285000  237.877500   
      MO     103.2525  109.4325  116.4925  ...  219.473750  213.193125  207.675000   
      MS     106.3325  112.3150  115.2700  ...  203.201250  200.736875  194.427500   
      MT     106.4825  105.8850  116.2000  ...  304.735625  305.946250  298.277500   
      NC     109.5025  113.6925  121.9300  ...  230.623750  230.080625  223.998750   
      ND      86.9800  100.8150  114.1250  ...  214.284375  222.262500  227.753750   
      NE     107.7475  110.2600  111.6275  ...  204.450625  202.480625  202.148750   
      NH     108.3750  117.1325  134.1175  ...  290.261875  272.996875  257.283125   
      NJ     110.7800  115.4950  131.7350  ...  358.349167  334.899167  309.972500   
      NM     126.0650  126.4175  125.7825  ...  250.833750  247.926875  238.030625   
      NV     120.3725  117.6700  116.6925  ...  278.077500  218.385625  172.669375   
      NY     129.4800  137.1250  160.0900  ...  308.687500  301.320625  290.381875   
      OH     101.1100  104.8700  106.8400  ...  191.458125  183.119375  177.550625   
      OK     119.0775  125.3575  120.8875  ...  190.124375  191.956875  192.590625   
      OR      98.1575   99.3400   98.1800  ...  345.050625  327.520000  297.331875   
      PA     105.5250  111.0675  116.9025  ...  241.154375  238.533750  233.283125   
      RI     104.6850  115.8625  124.3725  ...  346.040833  313.276667  288.813333   
      SC     119.0925  121.8425  125.4200  ...  230.531250  229.193125  222.107500   
      SD     101.2675  105.8625  112.2275  ...  228.110000  232.751250  233.503750   
      TN     106.2725  111.4225  114.1325  ...  220.612500  216.996875  210.468750   
      TX     124.8850  127.0900  127.9875  ...  193.720000  196.497500  195.833125   
      UT     111.2250  113.5950  113.2025  ...  310.841250  303.612500  275.786250   
      VA     108.2000  117.4800  123.3075  ...  293.028125  274.160000  259.735625   
      VT     113.6225  116.2950  123.1875  ...  269.546875  267.040625  262.430625   
      WA     103.9525  105.7300  108.7625  ...  321.473750  309.046250  282.813125   
      WI      95.6975  105.5475  107.2450  ...  244.130000  238.395000  231.125625   
      WV      97.0500   96.4475   95.0550  ...  201.718125  200.676875  195.403750   
      WY     111.7750  106.3550   98.8625  ...  281.516250  284.537500  274.730625   

      year         2010        2011        2012        2013        2014        2015  \
      state                                                                           
      AK     226.477500  229.212500  231.440625  238.128125  243.864375  254.059375   
      AL     201.412500  194.325000  195.036250  196.198750  200.078125  205.926875   
      AR     191.150625  188.447500  191.670625  195.375625  199.136250  204.042500   
      AZ     195.802500  176.876250  190.221250  215.875625  233.369375  250.040000   
      CA     227.798750  213.523750  216.597500  246.478125  274.621875  295.521875   
      CO     264.532500  255.703750  263.225000  281.538125  303.591250  334.508750   
      CT     230.266250  222.875000  218.453125  217.900625  218.898750  222.521250   
      DC     383.972500  391.901667  416.700833  460.618333  501.542500  537.642500   
      DE     267.504167  250.120000  245.470000  252.085000  256.952500  266.475833   
      FL     210.876250  197.276250  200.750000  216.693750  233.455625  256.181250   
      GA     192.088750  177.518750  175.465000  185.550000  197.910000  210.818125   
      HI     251.562500  242.819375  250.603750  268.958125  289.481875  309.120000   
      IA     204.945625  203.575625  207.501250  212.847500  217.978750  226.187500   
      ID     213.497500  194.597500  203.038750  218.008750  229.608125  245.776250   
      IL     201.736250  192.486875  190.706875  194.555000  200.761875  207.805000   
      IN     178.289375  175.982500  177.593125  181.718125  186.362500  194.011875   
      KS     196.817500  193.044375  195.898750  199.240625  206.163125  214.060000   
      KY     203.999375  200.170625  202.774375  205.940000  209.718125  218.289375   
      LA     219.535000  215.908750  219.605625  226.063125  232.538125  242.210000   
      MA     320.453750  314.159375  314.273750  325.828750  339.300000  355.134375   
      MD     258.255625  247.475000  247.556875  253.188125  258.164375  263.362500   
      ME     259.865625  255.480000  252.846250  256.501875  260.068750  269.965000   
      MI     166.404375  160.560625  163.946250  175.260000  187.625625  199.667500   
      MN     228.241250  217.573750  220.657500  232.610625  242.960000  254.315000   
      MO     201.246875  194.651250  196.059375  199.586875  205.003125  213.761875   
      MS     188.240000  185.051250  185.951875  188.286875  191.905000  198.484375   
      MT     286.760000  278.505000  286.218750  300.826250  313.103125  326.983125   
      NC     213.853125  205.196250  203.838750  207.976250  213.010625  223.301250   
      ND     233.946250  245.316250  265.945000  289.540000  310.246875  327.632500   
      NE     201.132500  200.840625  205.916250  213.693125  222.220625  233.150625   
      NH     247.735625  238.782500  236.174375  242.285000  250.808125  262.008750   
      NJ     298.190833  284.414167  278.352500  282.416667  290.167500  297.797500   
      NM     230.142500  219.441875  218.690000  222.657500  225.848750  231.953750   
      NV     154.348125  136.793125  138.748125  163.405000  184.851250  204.648125   
      NY     284.348125  278.873125  277.615000  281.405000  287.295000  296.278750   
      OH     173.121250  167.115000  168.588750  171.431875  177.818125  185.359375   
      OK     190.983750  187.922500  191.573125  196.123750  202.267500  211.614375   
      OR     275.556875  256.469375  259.171250  281.312500  303.131875  329.423750   
      PA     229.354375  224.678125  225.365625  229.303750  233.661250  240.118125   
      RI     275.976667  263.761667  258.303333  261.845833  271.773333  285.085000   
      SC     211.910625  202.655625  202.537500  206.403750  213.131875  225.105000   
      SD     231.628750  231.680000  236.988750  247.195625  256.611250  267.878125   
      TN     203.964375  199.144375  200.139375  206.434375  215.345000  226.691250   
      TX     194.401250  191.809375  197.264375  207.560000  220.986875  236.337500   
      UT     256.492500  240.588125  250.530000  272.536250  287.764375  306.211875   
      VA     250.259375  242.598125  244.223125  250.144375  256.516875  264.130625   
      VT     255.986875  254.092500  255.313125  257.278125  257.501875  262.476875   
      WA     266.009375  246.357500  246.263750  261.402500  277.551875  300.781875   
      WI     224.018750  216.218750  215.788125  219.214375  223.446250  232.371875   
      WV     194.330625  190.970625  195.295625  199.761250  206.249375  212.689375   
      WY     266.643125  263.085625  270.203750  277.328125  288.503125  299.831250   

      year         2016  
      state              
      AK     259.071250  
      AL     210.423750  
      AR     207.902500  
      AZ     262.140000  
      CA     311.207500  
      CO     358.622500  
      CT     223.870000  
      DC     557.011667  
      DE     270.530000  
      FL     274.356250  
      GA     220.463750  
      HI     323.162500  
      IA     231.470000  
      ID     257.168750  
      IL     212.070000  
      IN     199.340000  
      KS     220.678750  
      KY     224.556250  
      LA     248.896250  
      MA     365.742500  
      MD     268.698750  
      ME     275.486250  
      MI     207.235000  
      MN     262.641250  
      MO     220.911250  
      MS     203.181250  
      MT     337.501250  
      NC     231.276250  
      ND     330.597500  
      NE     239.858750  
      NH     269.321250  
      NJ     301.335000  
      NM     235.988750  
      NV     217.486250  
      NY     300.897500  
      OH     190.795000  
      OK     215.312500  
      OR     353.731250  
      PA     244.021250  
      RI     294.656667  
      SC     234.692500  
      SD     276.527500  
      TN     235.697500  
      TX     247.650000  
      UT     322.893750  
      VA     270.042500  
      VT     261.382500  
      WA     322.312500  
      WI     239.567500  
      WV     215.540000  
      WY     303.931250  

      [51 rows x 42 columns]

## Home price visualization 1

      plt.plot(df.year[df.state == 'NC'], df.price[df.state == 'NC'],color='blue')
      plt.plot(df.year[df.state == 'SC'], df.price[df.state == 'SC'],color ='orange')
      plt.plot(df.year[df.state == 'GA'], df.price[df.state == 'GA'],color ='r')
      plt.plot(df.year[df.state == 'VA'], df.price[df.state == 'VA'],color ='g')
      plt.plot(df.year[df.state == 'TN'], df.price[df.state == 'TN'],color ='olive')
      plt.plot(df.year[df.state == 'AL'], df.price[df.state == 'AL'],color ='springgreen')
      plt.plot(df.year[df.state == 'AR'], df.price[df.state == 'AR'],color ='pink')
      plt.plot(df.year[df.state == 'MS'], df.price[df.state == 'MS'],color ='sienna')
      plt.plot(df.year[df.state == 'FL'], df.price[df.state == 'FL'],color ='gray')

      plt.grid()
      plt.legend(['NC','SC','GA','VA','TN','AL','AR','MS','FL'])
      plt.title('Southern States Home Price')

![SouthernStatesHomePrice](https://user-images.githubusercontent.com/46631208/113437235-4a026780-93b4-11eb-807d-ec87152a5397.png)

      plt.plot(df.year[df.state == 'DE'], df.price[df.state == 'DE'],color='yellowgreen')
      plt.plot(df.year[df.state == 'NJ'], df.price[df.state == 'NJ'],color='b')
      plt.plot(df.year[df.state == 'NY'], df.price[df.state == 'NY'],color='springgreen')
      plt.plot(df.year[df.state == 'PA'], df.price[df.state == 'PA'],color='red')
      plt.plot(df.year[df.state == 'VT'], df.price[df.state == 'VT'],color='m')
      plt.plot(df.year[df.state == 'CT'], df.price[df.state == 'CT'],color='cyan')
      plt.plot(df.year[df.state == 'RI'], df.price[df.state == 'RI'],color='purple')
      plt.plot(df.year[df.state == 'MA'], df.price[df.state == 'MA'],color='orange')
      plt.plot(df.year[df.state == 'NH'], df.price[df.state == 'NH'],color='olive')

      plt.grid()
      plt.legend(['DE','NJ','NY','PA','VT','CT','RI','MA','NH'])
      plt.title('Northern States Home Price')

![NothernStatesHomePrice](https://user-images.githubusercontent.com/46631208/113437537-c9903680-93b4-11eb-8811-f548c9a1d74e.png)

      plt.plot(df.year[df.state == 'WA'], df.price[df.state == 'WA'],color='yellowgreen')
      plt.plot(df.year[df.state == 'OR'], df.price[df.state == 'OR'],color='b')
      plt.plot(df.year[df.state == 'CA'], df.price[df.state == 'CA'],color='springgreen')
      plt.plot(df.year[df.state == 'ND'], df.price[df.state == 'ND'],color='m')
      plt.plot(df.year[df.state == 'AR'], df.price[df.state == 'AR'],color='cyan')
      plt.plot(df.year[df.state == 'UT'], df.price[df.state == 'UT'],color='orange')

      plt.legend(['WA','OR','CA','NM','AR','UT'])
      plt.title('Western States Home Price')
      plt.grid()

![WesternStatesHomePrice](https://user-images.githubusercontent.com/46631208/113437580-dd3b9d00-93b4-11eb-812e-16ced84b1626.png)

      plt.plot(df.year[df.state == 'TX'], df.price[df.state == 'TX'],color='yellowgreen')
      plt.plot(df.year[df.state == 'NM'], df.price[df.state == 'NM'],color='b')
      plt.plot(df.year[df.state == 'OK'], df.price[df.state == 'OK'],color='springgreen')
      plt.plot(df.year[df.state == 'CO'], df.price[df.state == 'CO'],color='m')
      plt.plot(df.year[df.state == 'KS'], df.price[df.state == 'KS'],color='cyan')
      plt.plot(df.year[df.state == 'NE'], df.price[df.state == 'NE'],color='orange')

      plt.legend(['TX','NM','OK','CO','KS','NE'])
      plt.title('Mountain States Home Price')
      plt.grid()

![MountainStatesHomePrice](https://user-images.githubusercontent.com/46631208/113437624-f3495d80-93b4-11eb-82b4-595739f91b2c.png)

      plt.plot(df.year[df.state == 'OH'], df.price[df.state == 'OH'],color='yellowgreen')
      plt.plot(df.year[df.state == 'MI'], df.price[df.state == 'MI'],color='b')
      plt.plot(df.year[df.state == 'IL'], df.price[df.state == 'IL'],color='springgreen')
      plt.plot(df.year[df.state == 'MN'], df.price[df.state == 'MN'],color='m')
      plt.plot(df.year[df.state == 'IN'], df.price[df.state == 'IN'],color='cyan')
      plt.plot(df.year[df.state == 'ND'], df.price[df.state == 'ND'],color='orange')
      plt.plot(df.year[df.state == 'SD'], df.price[df.state == 'SD'],color='olive')

      plt.legend(['OH','MI','IL','MN','IN','ND','SD'])
      plt.title('Midwest States Home Price')
      plt.grid()

![MidwestStatesHomePrice](https://user-images.githubusercontent.com/46631208/113437659-065c2d80-93b5-11eb-86f8-67810c9cb9bc.png)

