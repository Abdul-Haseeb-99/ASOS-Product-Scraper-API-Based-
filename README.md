import requests
import random
import time
import pandas as pd

url = "https://www.asos.com/api/product/search/v2/categories/7113"

headers = {
    "user-agent" : "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36 Edg/144.0.0.0",
    "referer" : "https://www.asos.com/men/a-to-z-of-brands/adidas/cat/?",
    "accept" : "application/json"
}

payload = {
    "offset" : 72,
    "includeNonPurchasableTypes" : "restocking",
    "store": "ROW",
    "lang":"en-GB",
    "currency":"USD",
    "rowlength":4,
    "channel":"desktop-web",
    "country":"PK",
    "customerLoyaltyTier":"None",
    "keyStoreDataversion" : "qx71qrg-45",
    "limit" : 72,
}
offset = 0
limit = 72
res_tab = []
while True:


    payload["offset"] = offset
    res = requests.get(url, params=payload, headers=headers)
    data = res.json()
    products = data.get("products",[])

    if res.status_code == 200:
        print("processing")
    else:
        print(res.status_code)
        print("error loaded")
        break

    if not products:
        print("end")
        break

    
    for d in products:
         res_tab.append({
            "id" : d.get("id"),
            "name" : d.get("name"),
            "price" : d.get("price",{}).get("current",{}).get("text",{}),  
            "url" : d.get("url"),
            "brandName" : d.get("brandName")
        })
    
    print(len(res_tab))
    offset += limit
    time.sleep(random.uniform(1.5,3))  

print(len(res_tab))
df = pd.DataFrame(res_tab)    
df.to_excel("gigs.xlsx")


