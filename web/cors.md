# Cross-Origin Resource Sharing (CORS)

### CORS network flow

the user makes the OPTIONS request to the website, to check which domains are actually CORS allowed. once checked, it returns the CORS domains.

CORS only works on the frontend. that means, if the backend server tries to fetch() a resource from the domain that does not allow it to fetch, the backend server still be able to fetch the resource and CORS policy won't get triggered. whereas if the frontend server tries to make the same fetch request, the CORS policy will trigger and will not return the response.
