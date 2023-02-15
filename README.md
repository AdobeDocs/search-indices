# search-indices
Standalone solution for search index, product, and path-prefix mapping

#developer.adobe.com search guide Feb 2023:

## Enable search

### Franklin & Gatsby [aio-theme@4.7.11+]

Our app points search iframe source to:
 
https://developer.adobe.com/search-frame/ on prod,
https://developer-stage.adobe.com/search-frame/ on stage, and http://localhost:8000/ when running locally.

The iframed instance should be running https://github.com/AdobeDocs/search-iframe with enabled search using the Gatsby config in the next section.
<br><br>Note: The aio-theme layout component checks for `/search-frame/` pathPrefix to toggle the search iframe ready layout.

### Legacy Gatsby and the search-frame only

Add relevant [app id] and [api key] from Algolia to the repo's .env file

```
GATSBY_ALGOLIA_APPLICATION_ID=[app id here]
GATSBY_ALGOLIA_SEARCH_API_KEY=[api key here]
```


## Indexing 

### Configure product-index map

https://github.com/AdobeDocs/search-indices/blob/main/product-index-map.json

Format the index with it's relevant pathPrefix like so:

```
{               
 "indexName": "photoshop",
 "indexPathPrefix": "/photoshop/"
}
```

Find the relevant Product Name and add the index object to the productIndices array like so:


```
{
        "productName": "Adobe Photoshop",
        "productIndices": [
            {
                "indexName": "photoshop",
                "indexPathPrefix": "/photoshop/"
            },
            {
                "indexName": "uxp-photoshop",
                "indexPathPrefix": "/photoshop/uxp/2022/"
            },
            {
                "indexName": "uxp-photoshop-2021",
                "indexPathPrefix": "/photoshop/uxp/2021/"
            },
            {
                "indexName": "cis-photoshop-api-docs",
                "indexPathPrefix": "/photoshop/photoshop-api-docs/"
            },
            {
                "indexName": "photoshop-api",
                "indexPathPrefix": "/photoshop/api/"
            }
        ]
    },
```

If relevant product is missing add it to the top array.

### Franklin
Franklin indexer: https://github.com/adobe/adobe-io-website/tree/algolia-indexing-fix/.algolia

### Gatsby
The aio-theme indexer triggers during `yarn dev` and `yarn build` and beahves differently based on .env configuration.

Add relevant [app id] and [api WRITE key] from Algolia to the repo's .env file and the following variables

```
ALGOLIA_INDEXATION_MODE="index"
GATSBY_SITE_DOMAIN_URL="https://developer.adobe.com"
GATSBY_ALGOLIA_APPLICATION_ID=[app id here]
GATSBY_ALGOLIA_INDEX_NAME=[repo name]
ALGOLIA_WRITE_API_KEY=[api WRITE key]
REPO_NAME=[repo name]
```

`ALGOLIA_INDEXATION_MODE=[ "index" | "console" | "skip" ]` <br> "index" to push indices to Algolia <br> "console" to do a dry run in console <br> "skip" to skip indexing.


## Select index env (indexing and querying) [OPTIONAL - Mostly for testing and dev]

### Configure repo's .github/workflows/deploy.yml for deployment:

Add `GATSBY_ALGOLIA_INDEX_ENV_PREFIX: ${{ secrets.AIO_ALGOLIA_INDEX_ENV_PREFIX }}` to prod and dev build actions

### Configure repo's .env to test on localhost:

Add `GATSBY_ALGOLIA_INDEX_ENV_PREFIX=[ prod | stage | * ]` to .env 

### Legacy production indices support (no prefix):

Leave `secrets.AIO_ALGOLIA_INDEX_ENV_PREFIX` blank for deployment
Leave `GATSBY_ALGOLIA_INDEX_ENV_PREFIX` blank for testing on localhost
