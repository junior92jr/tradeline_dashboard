# Tradeline Dashboard

As the Dashboard already exists, I will asume the Dashboard uses `Bearer Token Authentication`. For the following endpoints explained in this document. We will consider that I must include the Bearer token in the Request Header.

## First Step: Display All Tradelines in the Dashboard

### Frontend

```bash
shipzero/dashboard/tradelines/
```

Considering "Tours" Section as a reference
We will display all items paginated with size of 10 items per page using a paginator

We need a new section in the SideBar from the Dashboard called "Tradelines".
Tradelines will display a grid table. 

We will have two mandatory Filter choice with a default value.
`Country to Country (Defaut choice)` or `City to City`


| by country (Default) | by city |
| (Optional) transport mode: road | rail | barge | sea | air |

|ID |Departure |Arrival |Transport Mode |Monthly Weight |Occu. per month |Distance in Km | Total C02     |
|---|----------|--------|---------------|---------------|----------------|---------------|---------------|
|---|----------|--------|---------------|---------------|----------------|---------------|---------------|

                                | 1 | 2 |3 ... 15 |                                           

### Backend

The default value for `location_type` will be `country`, pagination parameters has default values as well, `page=1` and `size=10`.

#### DEFAULT QUERY PARAMS
```bash
GET api/v1/tradelines/?page=1&size=10
GET api/v1/tradelines/?location_type=country&page=1&size=10
GET api/v1/tradelines/?location_type=city&page=1&size=10
```
#### OPTIONAL QUERY PARAMS
```bash
GET api/v1/tradelines/?page=1&size10&sort=tradeline_id
GET api/v1/tradelines/?page=1&size10&sort=monthly_weight
GET api/v1/tradelines/?page=1&size10&sort=ocurrences_per_month
GET api/v1/tradelines/?page=1&size10&sort=distance_in_km
GET api/v1/tradelines/?page=1&size10&sort=total_carbon_dioxide
```
mode_of_transport: road, rail, barge, sea, air
```bash
GET api/v1/tradelines/?page=1&size10&mode_of_transport=air
```

#### Response Example
```bash
{
    "items": [
        {
            "tradeline_id": "T34332342343",
            "current_mode_of_transport": "rail",
            "monthly_weight": 234.24,
            "ocurrences_per_month": 2,
            "distance_in_km": 16.072,
            "total_carbon_dioxide": 2.78,
            "location_departure": {
                "Country": DE,
                "City": Mainz
                "coordinates": {
                    "lat": 89.2342321,
                    "lng": 21.3315312
                }
            },
            "location_arrival": {
                "Country": ES,
                "City": Barceleona
                "coordinates": {
                    "lat": 89.2342321,
                    "lng": 21.3315312
                }
            }
        },
        {
            "tradeline_id": "T34532322345",
            "current_mode_of_transport": "road",
            "monthly_weight": 124.63,
            "ocurrences_per_month": 6,
            "distance_in_km": 26.072,
            "total_carbon_dioxide": 1.78,
            "location_departure": {
                "Country": DE,
                "City": Frankfurt
                "coordinates": {
                    "lat": 89.2342321,
                    "lng": 21.3315312
                }
            },
            "location_arrival": {
                "Country": ES,
                "City": Madrid
                "coordinates": {
                    "lat": 89.2342321,
                    "lng": 21.3315312
                }
            },
        }
    ],

    "total": 2,
    "page": 1,
    "size": 2,
    "pages": 1
}
```

## Second Step: Selecting a Row item

### Frontend 
```bash
shipzero/dashboard/tradelines/
```

When an Item is selected, a side Grid is displayed similar to "Tours".

We need to display a Map Pluggin that will Create a route based on a list of Coordinates. Additionally
We will display information of Each Coordinate in list. (Country or City Name).

### Backend
```bash
GET api/v1/tradelines/get-tradeline-stops/T34332342343/
```

#### Response Example
```bash
{
    "items": [
        {
            "order": 1,
            "country": DE,
            "city": Mainz
            "coordinates": {
                "lat": 89.2342321,
                "lng": 21.3315312
            }
        },
        {
            "order": 2,
            "country": FR,
            "city": Lyon
            "coordinates": {
                "lat": 19.2342321,
                "lng": 11.3315312
            }
        },
        {
            "order": 3,
            "country": ES,
            "city": Barcelona
            "coordinates": {
                "lat": 49.2342321,
                "lng": 11.3315312
            }
        },

    ]
}
```




## Thirt Step: When Opening a datailed view of a single tradeline

### Frontend

This step is not visible in "Tours" but I am asuming that when accesing to: 

```bash
shipzero/dashboard/tradelines/T34332342343
```

It would be possible to see a detailed view of the Tradeline by re using the frontend components already built for:

```bash
shipzero/dashboard/tradelines/
```

When opening a new section, We display a detailed view of the Tradeline. Because we don't need to display information from other items we can display extra information.

In one simply grid we display the Map with the points and the detailed information

```bash
(GET) api/v1/tradelines/T34332342343/
{
    "tradeline_id": "T34332342343",
    "current_mode_of_transport": "rail",
    "monthly_weight": 234.24,
    "distance_in_km": 16.072,
    "total_carbon_dioxide": 2.78,
    "carbon_dioxide_per_km": 8.3
    "ocurrences_per_month": 2,
    "location_departure": {
        "country": DE,
        "city": Mainz
        "coordinates": {
            "lat": 89.2342321,
            "lng": 21.3315312
        }
    },
    "location_arrival": {
        "country": ES,
        "city": Barceleona
        "coordinates": {
            "lat": 89.2342321,
            "lng": 21.3315312
        }
    },
    "contractor_list": [
        "CO42342344",
        "CO25234242",
        "CO23423444"
    ],
    "customer_list": [
        "CU42342344",
        "CU25234242",
        "CU23423444"
    ]
}
```

## Error Handling

If the backend service is not available or there is an unexpected error we should make sure we display proper status in the frontend Depending on the Status Code.

If Databasee or other internal services are not properly working
```bash
STATUS CODE: 503
{
  "detail": "Service is unavailable",
}
```

If endpoints are used without authentication
```bash
STATUS CODE: 401
{
  "detail": "Authentication required",
}
```

If tradeline id is not correct or does not exists
```bash
STATUS CODE: 404
{
  "detail": "Tradeline Not Found",
}
```
