# User Stories

## **User Stories for the Proposed Vehicle Data Management API**

### Introduction

The following user stories detail the core functionalities expected from the Vehicle Data Management API. These stories are prioritised to guide the development process and ensure that the proposed system is tailored to meet the requirements of different user roles, including 'Car Buyers', 'Sales Representatives', and 'Inventory Managers'.

#### 1. Vehicle Search (Car Buyer)

**As a car buyer, I want to search for a vehicle, so that I can find one that matches my preferences.**

**Acceptance criteria:**

* User can search by manufacturer.
* User can search by model.
* User can search by colour.
* User can search by fuel type.
* User can search by vehicle type.
* User can search by mileage.
* User can search by valuation.
* User can search for a vehicle with certain feature(s).
* User can search for a combination of the above properties.
* The search result shows the total number of cars that match the criteria.
* The search result should be paginated (page size of 100).

#### 2. Vehicle Editing (Sales Representative)

**As a sales rep, I want to edit a vehicle, so that I can make sure the information is correct ahead of the vehicle going on sale.**

**Acceptance criteria:**

* User can edit the manufacturer.
* User can edit the model.
* User can edit the colour.
* User can edit the fuel type (Options: Gasoline, Electric, Diesel, Hybrid).
* User can edit the vehicle type.
* User can edit the value (Range: £1000 to £100,000).
* User can edit the features.
* User cannot edit the mileage, registration mark, registration date, or VIN.

#### 3. Vehicle Addition (Inventory Manager)

**As an inventory manager, I want to add a vehicle, so that I can put new inventory on sale.**

**Acceptance criteria:**

* User can specify manufacturer.
* User can specify model.
* User can specify colour.
* User can specify fuel type.
* User can specify vehicle type.
* User can specify VIN.
* User can specify registration mark (Format: 2 uppercase letters, 2 numbers, 3 uppercase letters).
* User can specify vehicle registration date.
* User can specify mileage.
* User can specify valuation.
* User can specify feature(s).
* User cannot create a car with a VIN or registration mark that already exists.

#### 4. Vehicle Details Viewing (Sales Representative)

**As a sales rep, I want to view the full details of an individual vehicle.**

**Acceptance criteria:**

* User can look up a vehicle by a unique vehicle ID.
* User can see the value of every property of the vehicle.

#### 5. Vehicle Deletion (Sales Representative)

**As a sales rep, I want to delete a vehicle, so that I can remove sold vehicles from the system.**

**Acceptance criteria:**

* User can delete a vehicle by providing a unique vehicle ID.

### Summary

The user stories above detail the primary functionalities for the Vehicle Data Management API. They set specific, actionable requirements to aid the development process. By adhering to these user stories, the proposed API is able to facilitate the specified CRUD operations, while maintaining data integrity, and handle specific constraints (like unique VINs and registration marks) to ensure the reliability of the system.
