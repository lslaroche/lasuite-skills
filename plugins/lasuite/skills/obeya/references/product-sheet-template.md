# <Product name>

Local product sheet, read by the `obeya` and `posthog-insight` skills of the `lasuite` plugin. Not versioned: it holds internal ids.

## PostHog

* Project: `<id>` (<instance, e.g. EU>)
* Dashboards:
  * production: `<id>` (<URL>)
  * <other environments>: `<id>`

## Obeya

Post title: `### [<Product name> - OBEYA]`

### Tracked stat

Leave this section out if the product tracks no stat every week.

* Name: <e.g. WAU in production>
* Definition: <unit counted (e.g. unique `person_id`), event, filters>
* Reference: dashboard "<name>", insight "<name>"
* Start of the history: week of <DD/MM/YYYY> (<reason: e.g. when a tag first appeared>)
* Line format (French, as posted): `* <value> <label> (<+/-delta> vs la semaine précédente)`, with `🏆 ATH` at the end only on a record

### Query

Returns one row per week (Monday to Sunday), from the start of the history to the last complete week.

```sql
SELECT toStartOfWeek(timestamp, 1) AS week, uniq(person_id) AS value
FROM events
WHERE event = '<event>'
  AND <filters>
  AND timestamp >= toDateTime('<start of the history> 00:00:00')
  AND timestamp < toStartOfWeek(now(), 1)
GROUP BY week
ORDER BY week
```

## Known pitfalls

* <e.g. the insight ignores a filter, a tag only exists since a given date>
