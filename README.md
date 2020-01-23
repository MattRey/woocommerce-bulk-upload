# woocommerce-bulk-upload
Use python to bulk upload products without a paid plugin


Using zenfolio api to retrieve an xml file of the photos within a photo gallery, using this api interface - http://api.zenfolio.com/api/1.8/zfapi.asmx?op=LoadPhotoSet 

Save the corresponding xml file to 'photo_list.xml' in the same folder as your python code and run this to get an array of image names and an array of download links (for downlaodable products in your woocommerce photography store):

```
import xml.etree.ElementTree as ET
tree = ET.parse('photo_list.xml')
root = tree.getroot()[14]

img_array = []
link_array = []

for child in root:
    try:
        img_array.append(child[5].text)
        link_array.append(child[16].text.strip())
    except:
        print("something went wrong")
        
print(img_array)
print(link_array)
```
I created a single photo product with three variations (6x4 print, 10x8 print and digital download), exported it as a csv and then used that csv as the basis for the following, which uses the csv module to write the same structure of products for each and every filename in the img_array array (the range looks a bit wierd but that is simply the number of images you are selling multiplied by (number of product variations + 1) and step size of (number of product variations + 1):
```
import csv

with open('products.csv', mode='w') as products:
    product_writer = csv.writer(products, delimiter=',')
    x=0
    for n in range(1, len(img_array)*4, 4):
        product_writer.writerow([n, "variable","" , img_array[x], 1, 0, "visible", "", "", "", "", "taxable", "", 1, "", "", 0, 0, "", "", "", "", 1, "", "", "", "Uncategorized", "", "", "https://shop.littleshots.co.uk/app/uploads/2020/01/" + img_array[x], "", "", "", "", "", "", "", "", 0, "Size & Format", "10x8, 6x4, Download", 1, 1, "", ""])
        product_writer.writerow([n+1, "variation", "", img_array[x] + " - 10x8", 1, 0, "visible", "", "10x8 inch professional print in a card slip mount", "", "", "taxable", "parent", 1, "", "", 0, 0, "", "", "", "", 0, "", "", 12.00, "", "", "", "", "", "", "id:" + str(n), "", "", "", "", "", 1, "Size & Format", "10x8", "", 1, "", ""])
        product_writer.writerow([n+2, "variation", "", img_array[x] + " - 6x4", 1, 0, "visible", "", "6x4 inch professional print in a card slip mount", "", "", "taxable", "parent", 1, "", "", 0, 0, "", "", "", "", 0, "", "", 8.00, "", "", "", "", "", "", "id:" + str(n), "", "", "", "", "", 2, "Size & Format", "6x4", "", 1, "",""])
        product_writer.writerow([n+3, "variation, downloadable, virtual", "", img_array[x] + " - Download", 1, 0, "visible", "", "Single image download", "", "", "taxable", "parent", 1, "", "", 0, 0, "", "", "", "", 0, "", "", 15.00, "", "", "", "", "'-1", 30, "id:" + str(n), "", "", "", "", "", 3, "Size & Format", "Download", "", 1, img_array[x], link_array[x]])
        #product_writer.writerow([n, "variable", "", "", img_array[x], "", img_array[x]+" - 10x8"])
        x += 1
```

