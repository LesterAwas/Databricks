# Benguet Electrical Database
## Purpose
This database sources its data from power service advisories published in the official BENECO Electric Cooperative outage posts.

It is designed to support analysis of outage patterns and generate insights that can help reduce future interruptions and improve the quality and consistency of advisory postings.

## Scope

This database covers the electrical feeders and locations in the province of Benguet, Philippines. Included in the database's scope is

* Location, which includes the barangay(district) and municipality
* Feeder, including basic identifying information
* Interruption, which includes the post text, outage type, stard and restorations times and cause or purpose of the outage.

### Entities

The database includes the following entities:

#### Locations

The `location` table includes:

* `id` - an `INTEGER` which specifies the unique ID for the location. This column therefore has the `PRIMARY KEY` constraint applied.
* `barangay` - specifies the barangay or district/village of the location as `STRING`.
* `municipality` - specifies the municipality of the barangay as `STRING`.

#### Feeder

The `feeder` table includes:

* `id`- an `INTEGER` which specifies the unique ID for the feeder. This column therefore has the `PRIMARY KEY` constraint applied.
* `name` - specifies the name given to the specific feeder as `STRING`.

#### Interruptions

The `interruptions` table includes:

* `id` - an `INTEGER` that specifies the unique ID for the power interruption. This column therefore has the `PRIMARY KEY` constraint applied.
* `text` - an `INTEGER` that specifies the unique ID for the power interruption. This column therefore has the `PRIMARY KEY` constraint applied.
* `type` - specifies what type of interruption the outage falls under as `STRING`. The type can fall under `scheduled`, `unscheduled`, or `emergency`.
* `start` - specifies the date and time of the power interruption and is stored as `NUMERIC`.
* `end` - specifies the date and time when the power was restored and is also stored as `NUMERIC`.
* `remark` - this column specifies what the cause of the outage as `STRING`.
    * In the case of an unscheduled and emergency power outage, it will indicate the cause or incident leading to the outage. In the case of a scheduled power interruption, it will specify the maintenance activity done.
    * All power interruptions will have a cause(for unscheduled/emergency) or activity(scheduled). If the cause of the outage is still not determined, it should display "cause still to be determined" hence the `DEFAULT` constraint with the said text.

#### Supply

The `supply` table is a junction table between the `feeders` and `locations` table and includes:

* `feeder_id` - an `INTEGER` that represents the ID of the feeder supplying a location. This column references the `id` column in the `feeders` table and therefore has the `FOREIGN KEY` constraint applied.
* `location_id` -  an `INTEGER` that represents the ID of the location being supplied by a feeder. This column references the `id` column in the `locations` table and therefore has the `FOREIGN KEY` constraint applied.

#### Affect

The `affect` table is a junction table between the `interruptions` and `locations` table and includes:

* `interruption_id` - an `INTEGER` that represents the ID of the power interruption affecting a location. This column references the `id` column in the `interruptions` table and therefore has the `FOREIGN KEY` constraint applied.
* `location_id` -  an `INTEGER` that represents the ID of the location being affected by a power interruption. This column references the `id` column in the `locations` table and therefore has the `FOREIGN KEY` constraint applied.

### Relationships

The below entity relationship diagram describes the relationships among the entities in the database.

![ER Diagram](/Volumes/beneco_pipeline/bronze/data/ERD.png)

As detailed by the diagram:

* Interruption → Location (Many-to-Many)  
A single power interruption can affect one or multiple locations. For example, if a tree falls on a power line, all downstream locations supplied by that line may lose power. The `affect` junction table records which location(s) are impacted by each specific interruption.
* Location → Interruption (Many-to-Many)  
A location can experience multiple power interruptions over time. These may be caused by scheduled maintenance, line faults, or external factors such as fallen branches.
The `affect` junction table enables tracking of all interruptions that have impacted a given location.
* Location → Feeder (Many-to-Many)  
A location may be supplied by multiple feeders. This is common in systems with redundancy, where alternative supply lines are available if one feeder fails. In some cases, different parts of the same area may also be served by different feeders.
The `supply` junction table records which feeders can supply power to each location.
* Feeder → Location (Many-to-Many)  
A feeder can supply electricity to multiple locations within a distribution network.
The `supply` junction table tracks which locations receive power from each feeder.

