import requests

from bs4 import BeautifulSoup

import csv


def scrape_quotes():

    base_url = "http://quotes.toscrape.com/page/"

    quotes_data = []


    for page in range(1, 6):  # scrape first 5 pages

        url = base_url + str(page) + "/"

        print(f"Scraping {url}")

        response = requests.get(url)

        if response.status_code != 200:

            print(f"Failed to retrieve page {page}")

            break

        soup = BeautifulSoup(response.text, 'html.parser')

        quotes = soup.find_all('div', class_='quote')


        for quote in quotes:

            text = quote.find('span', class_='text').get_text(strip=True)

            author = quote.find('small', class_='author').get_text(strip=True)

            tags = [tag.get_text(strip=True) for tag in quote.find_all('a', class_='tag')]

            quotes_data.append({

                'text': text,

                'author': author,

                'tags': ", ".join(tags)

            })


    return quotes_data


def save_to_csv(quotes, filename='quotes.csv'):

    fieldnames = ['text', 'author', 'tags']

    with open(filename, 'w', newline='', encoding='utf-8') as f:

        writer = csv.DictWriter(f, fieldnames=fieldnames)

        writer.writeheader()

        for quote in quotes:

            writer.writerow(quote)

    print(f"Saved {len(quotes)} quotes to {filename}")


if __name__ == "__main__":

    quotes = scrape_quotes()

    save_to_csv(quotes)
