# Guide to Customizing Logseq Default Queries 🎯

## Quick Start Guide

### Step 1: Access Configuration
1. Open Logseq
2. Navigate to Settings > Config
3. Find and edit the `config.edn` file

![How to access config](https://user-images.githubusercontent.com/111847207/200155115-0344d96b-5982-43ed-b3cb-f40107b5b858.gif)

### Step 2: Why Customize?
By default, Logseq provides only basic query options:

![Default queries](https://user-images.githubusercontent.com/111847207/200147305-6fe68860-faef-4f53-bf42-4c5a11d3330c.png)

---

## Enhanced Query Pack 📦

This guide provides a powerful set of 7 task management queries:

| Query Type | Purpose | Tag |
|------------|---------|-----|
| 🔨 Working Tasks | Track current tasks | #NOW |
| 🐬 Active Projects | Monitor ongoing projects | #DOING |
| 📅 Future Tasks | View scheduled items | #LATER |
| ⚠️ Overdue Tasks | Show missed deadlines | - |
| 📊 10-Day Deadline | View upcoming deadlines | #TODO |
| ⏳ Pending Tasks | List waiting items | #WAITING |
| ⏰ Scheduled Tasks | Show 14-day schedule | #TODO |

```Clojure

:default-queries {:journals [

  {
    :title "🔨 Working Tasks #NOW"
    :query (task NOW)
    :collapsed? true
    :breadcrumb-show? false
  }
  {
    :title "🐬 Active Projects #DOING"
    :query (task DOING)
    :collapsed? false
  }
  {
    :title "📅 Future Tasks #LATER"
    :query (task LATER)
    :collapsed? false
    :breadcrumb-show? false
  }
  {
    :title "⚠️ Overdue Tasks"
    :query [:find (pull ?block [*])
            :in $ ?start ?today
            :where
            [?block :block/marker ?marker]
            (or
             [?block :block/scheduled ?d]
             [?block :block/deadline ?d])
            [(>= ?d ?start)]
            [(< ?d ?today)]
            [(contains? #{"NOW" "LATER" "TODO" "DOING" "WAITING"} ?marker)]
    ]
    :inputs [:180d :today]
    :result-transform (fn [result]
                       (sort-by (fn [d]
                                  (get d :block/deadline)) result))
    :collapsed? true
  }
  {
    :title "📊 10-Day Deadline #TODO"
    :query [:find (pull ?block [*])
            :in $ ?start ?next
            :where
            [?block :block/marker ?marker]
            (or
             [?block :block/deadline ?d])
            [(> ?d ?start)]
            [(< ?d ?next)]
            [(contains? #{"TODO"} ?marker)]
    ]
    :inputs [:0d :10d-after]
    :result-transform (fn [result]
                       (sort-by (juxt (fn [d] (get d :block/deadline)) result)
                       (sort-by (fn [h]
                                  (get h :block/deadline)) result))
    :collapsed? true
  }
  {
    :title "⏳ Pending Tasks #WAITING"
    :query (task WAITING)
    :collapsed? true
  }
  {
    :title "⏰ Scheduled Tasks #TODO"
    :query [:find (pull ?block [*])
            :in $ ?start ?next
            :where
            [?block :block/marker ?marker]
            (or
             [?block :block/scheduled ?d])
            [(> ?d ?start)]
            [(< ?d ?next)]
            [(contains? #{"TODO"} ?marker)]
    ]
    :inputs [:0d :14d-after]
    :result-transform (fn [result]
                       (sort-by (juxt (fn [d] (get d :block/scheduled)) result)
                       (sort-by (fn [d]
                                  (get d :block/scheduled)) result))
    :collapsed? false
  }

]}

```

## Configuration Options ⚙️

Each query supports these settings:
- `title`: Display name (supports emojis)
- `collapsed?`: Initial view state (`true`/`false`)
- `breadcrumb-show?`: Navigation path display (`true`/`false`)

> Last Updated: 2023/07/24

--- 

## Resources & References 📚

Need more help? Check these resources:
- [Official Advanced Queries Documentation](https://docs.logseq.com/#/page/advanced%20queries)
- [Community Query Pack](https://gist.github.com/psu/abf8d8c206f11d56c0e214d0bfcf065f#file-logseq-config-default-queries-edn-L50-L257)
- [Task Management Discussion](https://discuss.logseq.com/t/queries-for-task-management/14937)

---

- From [Column Layout plugin](https://github.com/YU000jp/Logseq-column-Layout)

