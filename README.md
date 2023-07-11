# furniture-database
Metadata from Furniture Products On-Sale for 363 sites.

This version is built on 10.07.2023 using
https://github.com/isgursoy/shopify-scraper, 
and you can see the format by the provided samples.

* The list of sites scraped is the shopify_sites.csv file.
At the time of build, some sites were down, or some took
extremely long to scrape everything.
So the actual list is the content list of the ./data directory 
from the database.tar.gz archive.

* Extracted size is 15.3 GB.

* Each JSON file here includes at least 1 product.

Never seen a 2.5 GB JSON file before? Here is how to read it without blowing up your RAM:
```python
import bigjson

def parse_site(filename: str):
	if filename.endswith(".json"):
		with open(os.path.join(get_database_path(), filename), 'rb') as json_file:
			if json_file.readable():
				json_stream = bigjson.load(json_file)
				site_key = json_stream.keys()[0]
				collection_names = json_stream[site_key].keys()
				print("Found", len(collection_names), "collections in", filename)

				with open(os.path.join(get_dataset_path(), site_key + "_index.json"), "w") as index_file:
					stripped_store_data = {}
					for collection_name in tqdm(collection_names):
						stripped_store_data[collection_name] = []
						for product in json_stream[site_key][collection_name]:
							stripped_product = {}
							stripped_product["title"] = product["Name"]
							stripped_product["price"] = product["Price"]
							stripped_product["category"] = product["Category"]
							stripped_store_data[collection_name].append(stripped_product)
					json.dump(stripped_store_data, index_file)
```
