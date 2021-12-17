## A Startup CTO’s Guide to the Modern Data Stack

Plugging your first BI tool on your first source of data (your production backend’s database) is already a step in the right direction. Well done!

Chances are you’ve tried both [Metabase](https://www.metabase.com/) and [Redash](https://redash.io/). Both being lightweight and open source alternatives to more full-fledged BI solutions like [Superset](https://superset.apache.org/) (also open source), [Looker](https://looker.com/), [Mode](https://mode.com/) or [Tableau](https://www.tableau.com/).

But if you’re lucky enough to get some traction internally on your brand new BI tool’s usage, you’ll soon end up with a sprawling collection of disparate queries and dashboards. Because everyone will be trying to find their own answers.

And that’s when things generally get messy… with other additional issues you’ll eventually encounter:

1.  Your production database won’t scale to handle the analytical workloads on top of your transactional ones.
2.  Your production database won’t contain the data of your whole business, only from the app it’s serving (no data from your CRM, ERP, ad spend, etc)
3.  Your production data won’t be ready for easy human consumption, quite the opposite, it should only care about being correctly normalized for your app’s consistent usage.

Now, don’t get me wrong, you can get away with those for some time, by “simply” vertically scaling your replica, by adding other data sources as additional “connections” to your BI tool, and by educating all data users on each and every separate data source available.

Or, you can take the next real steps on your analytics journey: bootstrapping your Modern Data Stack.

With this article, I want to provide some simple — albeit opinionated — next steps you should take to build your data-driven company. The idea is to prevent you from getting lost in [too much](https://a16z.com/2020/10/15/the-emerging-architectures-for-modern-data-infrastructure/) [detail](https://mattturck.com/data2020/)!

Personally, [I’ve been](https://www.linkedin.com/in/brice-luu-data-eng/) into data engineering for a few years now and have had the chance of setting up such stacks for a bunch of startups and scaleups over the past couple of years.

---

### Step 1: set up a cloud data warehouse
“Where they are put in boxes, and they come out all the same.”
![Photo by [Sikai Gu](https://unsplash.com/@gentle_kay?utm_source=hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=hashnode&utm_medium=referral)][1]

In order to decouple the scaling of your app’s transactional workloads from that of your analytics workload, the idea is to add a resource specifically tailored to the latter.

Furthermore, the aim will be to make it your central source of truth for what’s going on across your whole business.

In terms of options, general consensus among data engineers currently has it that you can’t go wrong with either [Big Query](https://cloud.google.com/bigquery/) or [Snowflake](https://www.snowflake.com/).

Big Query:

*   if you already rely on GCP for other stuff in your tech stack
*   or you have some machine learning use cases you already want to teeth out (as BQ is pretty tightly integrated with Google’s ML services, among others)
*   and/or you prefer a less hands-on approach to elasticity and scaling

Snowflake:

*   if none of the above really matters
*   and your team has some — even very limited — experience with database administration: you’ll feel right at home here (e.g user management through DDL statements)

---

### Step 2: extract & load into your warehouse
“Lock and load.”
![Photo by [Bernd Dittrich](https://unsplash.com/@hdbernd?utm_source=hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=hashnode&utm_medium=referral)][2]

Now that you have a brand new and immaculate data warehouse, it’s time to start loading things in it.

While we’re at it, why not dump every potentially useful data source from across your business?

Typically, your future rockstar CMO might be interested in measuring your ads’ ROI (i.e Is our spending on Facebook Ads leading new users to sign up? Or maybe bringing past ones back? …)

You have quite a few options in terms of SaaS providers to do precisely that.

What you’ll generally get, are click-based UIs that allow to quickly connect your database(s) and/or a bunch of third party APIs as data sources to be loaded into your warehouse.

This even allows less technical folks to quickly become independent in dumping the data from their favorite tool right into the common warehouse.

Here, I’d highlight [Fivetran](https://fivetran.com/) — current EL(T) market leader — as the more mature option, albeit currently *very* pricey.

Or [Airbyte](https://airbyte.io/) as the latest but strongest upcoming contender. Especially if you’re comfortable hosting you’re own ingestion infrastructure: it’s open sourced and just a `docker-compose up` away.

---

### Step 3: model, model, model
Also known as the ‘T’ in EL**T** (i.e “Transform”)
![Photo by [SwapnIl Dwivedi](https://unsplash.com/@momentance?utm_source=hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=hashnode&utm_medium=referral)][3]

What you’ll need now is to get usable results — KPIs — from all of that data. Maybe merging data across different sources, aggregating it per user segment, and/or per day, week, month, quarter? Comparing that year over year, or over the previous period?

There are quite a few specialist terms involved because of a long history of techniques used to do that holistically.

You might have heard of things like [Kimball](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/), [Dimensional modeling](https://en.wikipedia.org/wiki/Dimensional_modeling), [Star schema](https://en.wikipedia.org/wiki/Star_schema) (all three are often used interchangeably) or others: [Datavault](https://en.wikipedia.org/wiki/Data_vault_modeling), [Puppini bridge](https://towardsdatascience.com/the-new-unified-star-schema-paradigm-in-analytics-data-modeling-review-a245b2641dc8), etc.

These techniques have roots back in the 80s’ when on-premises proprietary data warehouses were the norm (and E**T**L was a necessary evil). So the arguments focused on performance are way less relevant nowadays with cloud data warehouses being the norm.

But all the other benefits remain essential.

The problem is, in a start up environment where everything is going too fast, taking 6 months to design that perfect star schema from scratch will rarely be an option.

Hence my general advice in that context is: start from the use case and work your way back to the data sources (not the other way around).

Then you’ll refactor into more generic and intermediate layers/models once multiple — think [rule of three](https://en.wikipedia.org/wiki/Rule_of_three_%28computer_programming%29) — use cases need similar transformation logic applied upstream.

A framework that really helps pinning all of that down is [dbt](https://www.getdbt.com/).

How does it help ?

It allows anyone that speaks SQL to contribute to your shared data warehouse construction. All of that in a versioned, tested and templated way, with builtin dependency resolution between all your models and sources.


### Then, what’s next?
Give back!
![Photo by [Damir Spanic](https://unsplash.com/@spanic?utm_source=hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=hashnode&utm_medium=referral)][4]

This one should actually go hand in hand with that previous step. Focusing on the actual use case(s) is what will align the data team to quickly bring strong value to the table.

This can take multiple forms depending on the use case at hand:

*   from dashboards (or alerts) to allow the whole company to gain insight on the business
*   to [reverse ETLs](https://medium.com/memory-leak/reverse-etl-a-primer-4e6694dcc7fb) that’ll make that transformed data directly actionable by sharing it back to the operational tools the other teams are using (e.g calculate your segments in your data warehouse to then (re)load them in your email marketing tool, weighing clients at risk for your CRM, …)
*   or serving data APIs to feed some insights/recommendations/predictions into your product directly


### Epilogue

Something I could’ve mentioned: of course you’ll need to start recruiting some data specialists along the way. A data analyst or two for starters, helped by an [analytics engineer](https://blog.getdbt.com/analytics-engineering-for-everyone/).

I’ve held back on that one to highlight the fact that: there are quite a few things you *can* progress on, even before that very first data hire! That would definitely help with the hiring process, by conveying the importance you want to place on having a sustainable data stack. And it could actually enable them to make an impact from the get-go.

There you have it. You’re just a few steps away from data-enabled greatness! So start building ;-)

![Photo by [Randy Fath](https://unsplash.com/@randyfath?utm_source=hashnode&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=hashnode&utm_medium=referral)][5]

[1]: https://cdn.hashnode.com/res/hashnode/image/upload/v1639571680778/XTeb9w0zX.jpeg
[2]: https://cdn.hashnode.com/res/hashnode/image/upload/v1639571682459/gOjKL3u3J.jpeg
[3]: https://cdn.hashnode.com/res/hashnode/image/upload/v1639571684061/FunrXIG-B.jpeg
[4]: https://cdn.hashnode.com/res/hashnode/image/upload/v1639571685661/-Uwi-1Tt7.jpeg
[5]: https://cdn.hashnode.com/res/hashnode/image/upload/v1639571687153/aEHIowJsl.jpeg