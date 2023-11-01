# NikeShoes_WebScraping
In this Python web scraping code, we will define the url by user input preference to find THE shoes for users. Then we will further analysize the shoes type and price for man / woman shoes by a general web scraping.

## Part 1: Web Scraping with User Preference
### with this interactive coding, user will then get the preferred list of Nike shoes based on gender/ shoes type/ budget.
1. User Input: Gender
<br />We ask users to input man/ woman
```ruby
gender = input("What are you looking for today? Man or Woman product? ")
```

2. User Input: Shoes Type
<br />We first print out the list of available shoes type of the selected gender, then we ask users to input the preferred shoes type
```ruby
shoes_type_list = {'Jordan':'jordan','Dunk':'nike_dunk','Air Force':'airforce1','Air Max':'air_max_collection','Zoom':'zoom','React':'react'}
keys = list(shoes_type_list.keys())
keys_line = ', '.join(keys)
print("Available Shoes Type: ", keys_line)

shoes_type_key =input("which type of shoes you are looking for? (please copy from the above list)")
shoes_type = shoes_type_list[shoes_type_key]
```

3. User Input: Budget
<br />We ask users to input their min and max price range. If users input non-numerical value, it will prompt the users to input again.
```ruby
while True:
    try:
        min_budget = float(input("What is your price range? Min: "))
        break  # Break out of the loop if input is a valid numeric value
    except ValueError:
        print("Invalid input. Please enter a numeric value.")

while True:
    try:
        max_budget = float(input("What is your price range? Max: "))
        break  # Break out of the loop if input is a valid numeric value
    except ValueError:
        print("Invalid input. Please enter a numeric value.")
```

4. Get the url
<br />Since the nike website requires curser hovering to trigger automatical page loading for more products above 36 items, we will use a loop to iterate over multiple pages.
<br />In each loop for each page, we will append the shoes data to a list.
```ruby
total_pages = 5
shoes_data = []

for page in range(1, total_pages + 1):
    page_url = f'https://www.nike.com.hk/{gender}/{shoes_type}/shoe/list.htm?intpromo=PNTP&page={page}&limit=36'
    page_html = requests.get(page_url).content
    page_soup = soup(page_html, "html.parser")

    # Extract the shoe details from the page
    shoes_list = page_soup.find_all('dl', {'class': 'product_list_content'})
    for shoe in shoes_list:
        name = shoe.find('span', {"class": 'up'}).text.strip()
        price = shoe.find('dd', {"class": "color666"}).text.strip()
        price = float(price.split('HK$')[-1].replace(',', ''))
        if min_budget <= price <= max_budget:
            shoes_data.append({'Name': name, 'Price': price})

    time.sleep(1)
```

5. Convert to DataFrame
<br />Lastly, we put the data into a data frame for better visualization with price sorting.
```ruby
shoes_data = pd.DataFrame(shoes_data).sort_values('Price')
shoes_data
```


## Part 2: Web Scraping with Data Visualization
### with this scraping coding, user will then get the visualization on the shoes type and prices of the preferred Nike Shoes (men / women).
1. Web Scraping with Gender preference
<br />We follow the similar practice from Part 1 with only gender input and generate a list of Nike Shoes.

2. Shoes Type Detection
<br />We added the Shoes Type detection Function to categorize the shoes list:
```ruby
def detect_shoe_type(product_name):
    for shoe_type in allshoes_type_list:
        if shoe_type.lower() in product_name.lower():
            return shoe_type
    return 'others'
```
3. Bar Plot to show the distribution of different shoes type:
```ruby
shoe_type_counts = filtered_df['Shoe Type'].value_counts()
plt.bar(shoe_type_counts.index, shoe_type_counts.values)

plt.xlabel('Shoe Type')
plt.xticks(rotation=45)
plt.ylabel('Count')
plt.title('Number of Shoes by Type')

# show the numbers of shoes under each type
for i, count in enumerate(shoe_type_counts.values):
    plt.text(i, count, str(count), ha='center', va='bottom')
plt.show()
```

3. Box Plot to show the price range and variation of different shoes type:
<br />Boxplot provides a visual summary of the distribution, including median, quartiles, and any outliers. It can help identify the range, spread, and skewness of the price data.
```ruby
sns.boxplot(x='Shoe Type', y ='Price',data=filtered_df)
```

4. More statistical summary of the price distribution
```ruby
filtered_df.groupby('Shoe Type')['Price'].describe()
```
