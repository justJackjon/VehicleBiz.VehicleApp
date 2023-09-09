# Functional Requirements Overview

## **Functional Requirements and Dataset Analysis for the Proposed Vehicle Data Management API**

### Introduction

The Vehicle Data Management API is proposed as a tool to facilitate efficient interactions with vehicle inventory data. The primary aim is to replicate the management of the entire vehicle sales lifecycle, from the moment a vehicle is added to the inventory until its eventual sale. To determine the functional requirements of this API, [user stories](functional-requirements/user-stories.md) were provided, and a thorough [analysis](./#dataset-analysis) of a [sample dataset](./#example-vehicle-data) was performed. Although the dataset is fictional, it serves to represent the kind of information and scale anticipated in a genuine external-facing product. By treating the design and development of this API as if it were destined for real-world applications, the goal is to demonstrate best practices and high-quality API design, while considering the budgetary limitations of this being a personal portfolio demonstration application.

### Core Functionalities

Based on the dataset and the associated [user stories](functional-requirements/user-stories.md), the following core functionalities are identified for the Vehicle Data Management API:

1. **Vehicle Search:** Allows users to search vehicles based on various criteria such as manufacturer, model, colour, fuel type, mileage, valuation, and features.
2. **Vehicle Editing:** Sales representatives can edit details of the vehicles to ensure accurate representation. However, certain fields like mileage, registration mark, registration date, and VIN remain immutable.
3. **Vehicle Addition:** Inventory managers can add new vehicles to the inventory, however, data must be validated to ensure that the VIN and registration marks are unique.
4. **Vehicle Details Viewing:** Users can view comprehensive details of a vehicle using a unique vehicle ID.
5. **Vehicle Deletion:** Enables sales representatives to remove vehicles that have been sold or are no longer available in the inventory.

### Dataset Analysis

The sample dataset comprises a collection of 100,000 vehicle entries. Each entry provides detailed information about a vehicle, including:

* Manufacturer (32 unique manufacturers)
* Model (60 unique models)
* Color (31 unique colors)
* Fuel type (4 types: Gasoline, Electric, Diesel, and Hybrid)
* Vehicle type (11 unique types, with 'Minivan' being the most frequent)
* Vehicle Identification Number (VIN) - Unique for each vehicle
* Registration Mark - Unique for each vehicle, adhering to the format: 2 uppercase letters, 2 numbers, 3 uppercase letters.
* Registration Date
* Mileage (ranging from 18 to 250,000 miles with an average mileage of approximately 125,312 miles)
* Valuation (ranging from £1,001.65 to £99,999.37 with an average valuation of approximately £50,426.58)
* Features (a list of features associated with the vehicle, e.g., "Leather Seats")

### Example Vehicle Data

| Manufacturer | Model      | Color  | Fuel     | Type                | VIN               | Registration Mark | Registration Date        | Mileage | Valuation | Features                                               |
| ------------ | ---------- | ------ | -------- | ------------------- | ----------------- | ----------------- | ------------------------ | ------- | --------- | ------------------------------------------------------ |
| Chrysler     | Colorado   | orchid | Gasoline | Sedan               | D7EPHPD8STMZ89414 | GN04FDP           | 2022-10-11T00:35:24.523Z | 216906  | 73153.85  | \["Leather Seats"]                                     |
| Cadillac     | Expedition | violet | Electric | SUV                 | F6AUZSMLWANH86964 | GM47HUI           | 2010-04-28T00:17:03.533Z | 66567   | 20684.58  | \[]                                                    |
| Jeep         | Roadster   | gold   | Gasoline | Cargo Van           | 2A8X5BNE3AWD48236 | VD31ZJR           | 2014-09-17T19:48:34.023Z | 114848  | 68434.02  | \["Heated Seats","Towbar","Parking Sensors","Sunroof"] |
| Polestar     | Explorer   | plum   | Hybrid   | Extended Cab Pickup | BZ2JHLUG60S327958 | KJ41GOV           | 2009-10-21T06:24:51.242Z | 149861  | 44723.73  | \["Cruise Control"]                                    |
| Nissan       | Aventador  | purple | Gasoline | Wagon               | KT5BB2X72ZY516511 | US79EVF           | 2010-08-13T23:13:20.998Z | 105772  | 92495.43  | \["Leather Seats"]                                     |

### Summary

Understanding the functional requirements is paramount to designing and developing a system that caters to the needs of all potential stakeholders. By making design decisions based on the analysis of the sample dataset and a thorough understanding of anticipated user interactions, an informed approach to design and development can be undertaken.
