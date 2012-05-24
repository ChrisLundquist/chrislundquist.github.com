---
layout: post
title: "Everything You Were Never Told About Billing Code"
category: Development
tags: []
---
{% include JB/setup %}

Most of these may seem fairly self evident when written here,
but they are often easy to overlook when in the middle of coding.

The most important things about billing code are:
* Results are reproducible and consistent
* Precision over Accuracy
* Invoices always have sub items.

### Consistency is key
The most common issue I see is having the price of an object change at some
point in the future, which in turn causes the amount of every invoice in the
past to change with it. Sometimes this happens because of sales or promotions,
but usually it goes unnoticed until there is an issue with the purchase, then
a nightmare follows when records are misaligned. A wise coworker once told me
a great secret to not piss people off. "Don't fuck with people's money". Words
worth their weight in gold so to speak.

### Precision over Accuracy
Precision means getting the same answer each time, even if it is not quite the
right answer. People will not care how you choose to round tax and the like
as long as it looks close enough. They will care when you tell them their bill
is a slightly different number each time.

The greatest example I have of this is a particular bit of software known as
masteringphysics.com . MasteringPhysics pretends to value precision by telling
you to round using significant figures, and nagging you whenever you enter too
many digits of precision. Invariably, as soon as you follow what they say the
answer was supposed to be for the next question in the series, they give you
the helpful, "Your answer was close, but not quite right, check your rounding"
when you used their supplied answers opposed to your more exact answers.

At the other end of the spectrum I, having learned this lesson from
masteringphysics.com, and with working with an invoice similar to a phone bill
decided to add the length of each phone call using millisecond precision
and round it at the end to get the most accurate answer. This however lead
to problems when the customer decided we were adding a few seconds to their
bill since the invoice only displayed length up to the second. So all the
few extra milliseconds here and there were not shown on the invoice.

Worse yet, if you took any subset and added them you would get a different
total than displayed on the invoice. Again due to using more precision than
shown.

### Invoices Always Have Sub Items
Your phone bill has records of each call.
Your grocery bill has records of each item.
I am unable to think of an invoice not in this format. More importantly
they never have more than one layer of sub items. People don't want to
resolve dependency trees. They just want to know what they were billed for
and what credits they got.

The only exception there might be to this rule is the medical industry as
there were talks given at RailsConf 2012 by a company that specialized in
medical billing. Given that people are willing to pay someone else to bill
for their services. It must be really painful to do.
