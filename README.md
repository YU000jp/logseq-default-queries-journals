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

> [!WARNING]
Those codes does not work with Logseq db version.

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
;; Default queries configuration for Logseq journals
 :default-queries {:journals [
   {
    ;; Simple query: Find all NOW tasks
    :title "🔨 Working Tasks #NOW"
    :query (task NOW)
    :collapsed? true
    :breadcrumb-show? false
  }
  {
    ;; Simple query: Track ongoing project tasks
    :title "🐬 Active Projects #DOING"
    :query (task DOING)
    :collapsed? false
  }
  {
    ;; Simple query: List scheduled future tasks
    :title "📅 Future Tasks #LATER"
    :query (task LATER)
    :collapsed? false
    :breadcrumb-show? false
  }
  {
    ;; Advanced query: Find overdue tasks from last 180 days
    :title "⚠️ Overdue Tasks"
    :query [:find (pull ?block [*])  ;; Pull all block properties
            :in $ ?start ?today      ;; Input parameters: start date and today
            :where
            [?block :block/marker ?marker]  ;; Find blocks with task markers
            (or                             ;; Match either scheduled or deadline
             [?block :block/scheduled ?d]
             [?block :block/deadline ?d])
            [(>= ?d ?start)]               ;; Date >= start date
            [(< ?d ?today)]                ;; Date < today
            [(contains? #{"NOW" "LATER" "TODO" "DOING" "WAITING"} ?marker)]  ;; Task types to include
    ]
    :inputs [:180d :today]  ;; Look back 180 days from today
    :result-transform (fn [result]  ;; Sort results by deadline
                       (sort-by (fn [d]
                                  (get d :block/deadline)) result))
    :collapsed? true
  }
  {
    ;; Advanced query: Show tasks due within next 10 days
    :title "📊 10-Day Deadline #TODO"
    :query [:find (pull ?block [*])
            :in $ ?start ?next       ;; Input parameters: start and end dates
            :where
            [?block :block/marker ?marker]
            [?block :block/deadline ?d]  ;; Only check deadline
            [(> ?d ?start)]                ;; Date > start date
            [(< ?d ?next)]                 ;; Date < end date (10 days from now)
            [(contains? #{"TODO"} ?marker)] ;; Only TODO tasks
    ]
    :inputs [:0d :10d-after]
    :result-transform (fn [result]
                       (sort-by (juxt (fn [d] (get d :block/deadline)) result))
                       (sort-by (fn [h]
                                  (get h :block/deadline)) result))
    :collapsed? true
  }
  {
    ;; Simple query: List tasks waiting for input or external factors
    ;; Use this for tasks that are blocked or dependent on others
    :title "⏳ Pending Tasks #WAITING"
    :query (task WAITING)
    :collapsed? true
  }
  {
    ;; Advanced query: Find scheduled tasks for next 14 days
    ;; Useful for planning and calendar view
    :title "⏰ Scheduled Tasks #TODO"
    :query [:find (pull ?block [*])        ;; Pull all block properties
            :in $ ?start ?next             ;; Input parameters: start and next 14 days
            :where
            [?block :block/marker ?marker]  ;; Find blocks with task markers
            (or
             [?block :block/scheduled ?d])  ;; Only check scheduled date
            [(> ?d ?start)]                ;; Date > start date (today)
            [(< ?d ?next)]                 ;; Date < end date (14 days from now)
            [(contains? #{"TODO"} ?marker)] ;; Only show TODO tasks
    ]
    :inputs [:0d :14d-after]              ;; Set date range: today to 14 days ahead
    :result-transform (fn [result]         ;; Sort results by scheduled date
                       (sort-by (juxt (fn [d] (get d :block/scheduled)) result))
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

