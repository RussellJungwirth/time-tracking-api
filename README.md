# time-tracking-api
RESTful api for time tracking log entry CRUD and aggregate reporting


### roadmap

```

use cases for time tracking app:

time entry - sequential.  group entries by category

days activities are grouped by description, sum of duration

categories correlate to work tracking entities ("tickets").  they include a total time value.  duration for all time entries logged to the category are subtracted from total time to create a "running total" metric.

for any given day time tracking entries should be available for integration into a work tracking system through manual entry or api level integration. recommend a template-driven format for time entry display.  should support any metrics identified above, most notable is the itemized list of time entries associated with that category on a given day.


word-click based entry support?

javascript driven web app.  time tracking entries are saved in a json format on a flexible storage back-end

ci/cd driven development and deployment

implement it as a REST API so it can be used with different back-ends and front-ends
use a data layer interface that allows us to switch out the back-end to relational, nosql, flatfile etc.


roadmap:
1. simple list editor (the time tracking table)
list values: 
	assigned key - a guid variant guaranteed to be unique across all records
	owner key - for grouping data by tenant in the future. use same value for all data in your application database.
	start timestamp - unique key for contextual ordering
	end timestamp - optional - assigned to just before next event's start time when not available
	description
	tags - future - list of tags associated with the entry

list is automatically ordered by timestamp
list ordering controlled in code as sort order (sort order is restricted by use case and calculations)
list entries can be changed
list entries can be added
list supports multiple attributes
list entries support timestamp data type
some rules around start and end time
	start time required.
	end time not required
	if end time not set, end time is defined by next element in the list
all list entries assumed to be in same time zone
list ordered by timestamp

2. list is persisted to a file
loaded at startup
saved when changed
has basic schema validation through ajv

3. tagging
list entries can be associated with one or more discrete tags
available tags is a hard-coded list and presented as unique in UI
rules for tags are driven by json spec.  tags align to keys.  valid key == valid tag
grand totals displayed by tag at day and overall levels

4. group entities (work_group table)
drives the reporting requirements
groups correlate to work tracking entities commonly known as billing jobs, cost centers
groups are a separate type of list (separate table)
groups are able to be edited, added, deleted
groups have similar set of fields to time entries

5. reporting
time entry to group resolution
	the mapped group set is resolved at the day level.  resolution can be for all tags or a subset of tags
	for each tag, walk through the list and get best match based on filter criteria (javascript filter and sort map functions)
	each time entry is mapped to only one group
	all tag matches, pick by time range bounds, latest start date, latest end date, list order

	include audit information in API responses so easy to verify rules

	subtotal section per day and globally for all groups in scope
		includes remaining time metrics based on running total

6. template driven subtotals
	supports variable interpolation in text string to include metrics, description lists etc
	must support current formats in my time tracking system
	aggregate tracking entries by description

7. api integration layer for time tracking systems (stretch goal)




store dates as epoch so easy to compare

data structure is partitioned by day to limit extend of cascading impacts

running totals will get aggregated to day level then resolved to global level

tag-based link between time entries and groups means that tag associations can change.
tags are not unique.  multiple tags can be linked to a single group.  group resolution is evaluated at run time  if multiple tags it picks the one in the time range.  time ranges are assigned at load time.  if multiple in same time range, latest start date.  if same start date, latest end date.  if still duplicate pick first one in list by order.  time entries are counted against only one group

enforce limits on data volumes to prevent excessive processing load.
limits:
  number of time entries
  unique tag count
  groups

when limit is exceeded split out by date range to separate files

add support for identifying groups that do not resolve in current list.  makes it easy for user to prune out stale groups.  maybe even prioritized by "staleness"?

consider how this project can be scaled out into a software service (think planit poker).  identify some features on such a roadmap.

since this is driven by an API, we should support a bulk load facility.  the data set is not all that big is it?



```

resource links:

- [json schema](https://ajv.js.org/)
- [javascript aggregates](https://stackoverflow.com/questions/11199653/javascript-sum-and-group-by-of-json-data)
- [electron todo list](https://codeburst.io/build-a-todo-app-with-electron-d6c61f58b55a)
- [markdown basic syntax reference](https://www.markdownguide.org/basic-syntax/)




