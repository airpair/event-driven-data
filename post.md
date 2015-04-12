Over the past decade, I've viewed the storage of data in a single frame of mind: data are mutable. A piece of information was always inserted, updated N number of times over its lifetime, and eventually deleted and erased from history. The data was always true and always current. Any attempt to store or retrieve history on the data was a nice-to-have feature, with varying implementations to very specific pieces of the information for which the database was responsible, therefore limiting its scope and complexity. But what if we took this viewpoint and turned it on its head? What if, instead of historical information being an afterthought, we made it a first-class citizen of the database and designed a solution around it? Instead of storing ever-changing data, we stored immutable events which the application could dynamically interpret and present back to the user in whatever way was needed?

## Getting Started ##
My journey down the path of immutability started on the recommendation of our architect, Emmett. We had just started design on a new project and were contemplating moving away from the typical relational database we'd used for many years to a less expensive alternative. His thought was that with the change in technology we may be able to implement a more unique approach to storing the data. We all met up one afternoon in conference room D and Emmett immediately kicked things off.

"We have always treated data as completely mutable. Let's take an address as a very simple example." At this point he walked to the board and picked up a marker. 
## The Mutable Address ##
"In 2010 someone, let's call him Marty, adds his mailing address into the system," he said, drawing a small table containing the standard pieces of address information that we had come to be very familiar with over the years. He continued, "Over five years Marty changes his address ten times due to being a musician that can't settle down. At any point in time during the five years I could have easily queried the database and found what Marty's current mailing address was. Let's say that after five years someone needed to know where exactly Marty was living on November, 5th 2012." 

![Mutable Address Example](http://drive.google.com/uc?export=view&id=0B9EBxo1jLWh4TThoQjlHNU1Qc00 "Mutable Address Example")

Emmett drew a giant question mark on the board and went on, "Or how about when that update occurred?" He drew another question mark. "Or how about the time the time that he stole my Delorean and disappeared for two months only to show up on the other side of town wearing the same clothes with my car hanging halfway out of the old theater that's now a church?" A third question mark made its way onto the board, followed by a few exclamation points as well. Things had gotten weird.

Emmett took a deep breath and continued, "The simple answer is that without a significant change to the application and/or database these answers would be left unsolved."

After a moment of silence he looked around and exclaimed, "Great Scott! Someone took all of the erasers again," and proceeded to wipe the board clean using his hands.

## Immutable Address Events ##
"Now let's take the same scenario but imagine that instead of a single record we stored the same information as a series of immutable events." Emmett proceeded to draw the same record up on the board again. "When the mailing address was initially added into the system in 2010, this would have been tracked as an event, say 'new'." 

He added a column to the existing address fields labeled "EVENT" and within it wrote "NEW" and continued, "Each change over the next five years could have been inserted as a new "moved" event, each time inserting a new copy of the mailing address along with information about who issued the change, from where it originated, etc." Under the initial address he added another series of blocks to represent multiple inserts of address information, marking each record's event column as "MOVED". 

Once he was finished writing the new addresses he proceeded with his explanation, "Once an event is entered into the database it is never deleted or altered in any way, making the data we have for Marty immutable. With the data tracked as events such as this, we can easily answer any of the questions I asked before." And with that he scratched an itch on his face, marking it with the black dry-erase marker that had transferred to his hands when he wiped off the board earlier.

![Address Changes as Immutable Events Example](http://drive.google.com/uc?export=view&id=0B9EBxo1jLWh4UzVwaXRRX1duUkk "Address Changes as Immutable Events Example")

"I'm still not seeing how this is better than us just having the application keep track of changes in a history log," remarked Buford, one of the junior members of the development team.

"Well Buford," Emmett replied, "by changing our data in this way we've gained a significant advantage over how we typically manage information: the ability to travel through time."

At this point in the conversation and by the look in his eyes we were sure that Emmett should probably have called in sick that day, but he ran his fingers through his prematurely silver hair and continued with his explanation.

"By replaying the events that we recorded we can easily see what Marty's address information was at any point in time, giving users of the application the ability to do much more with the data than was ever possible with just a single mutable address rec-."

Buford cut across Emmett, "Hold on a second. I'm still not seeing how this is any better than what we already have."

## Fixing Bugs Without Fixing Data ##
Emmett narrowed his eyes at Buford and continued, "Well before I was interrupted I was about to get to the best part. Let's say that the logic that we write to replay a particular event is released to production with a bug, and if you're working on the code Buford the likelihood of that happening is greater than not. Say that you introduced a bug when the application replayed the 'MOVED' event that accidentally transposes what we have in the line one and line two fields of the address prior to returning it to the caller. Now customers are seeing this data about addresses that doesn't make sense to them. In the past this would have required not only a fix to the application but also someone to create and run a datafix for all of the records affected by this bug to straighten out the information in the database as well."

As Emmett continued to explain the example he started to pace around the room. "Now let's take that same situation and apply it to this event-driven data model. Customers are calling in because of the bug and what do we do now? We still need to fix the bug. This is obvious. But because we've corrected a bug in the way that the event was processed and rendered to the user, fixing the bug is the only thing that development needs to do. Because the data that we've stored is simply a list of events, the logic behind replaying them can be changed at any point to present our users with new or corrected ways of seeing their data. Effectively we can change an action in the past to alter the present!"

With this Clara, the attendee from customer support gave a verbal whoop which took everyone by surprise. 

## Analyzing Data Through Time ##
George from marketing who had crashed the meeting uninvited took this opportunity to try to bring Emmett's jubilation crashing down. He straightened his necktie and said, "What marketing is interested in is analytics about who is using our system, how they're using it, and how everything correlates to one another. While all of this event stuff sounds wonderful, I'm not sure how it benefits my team in the long run."

Emmett took a moment to gather his thoughts and responded, "How do you gather such information now?"

"Well we have direct access to the production database of course. Anyone on my team can write a query in a few minutes and see who is purchasing the most widgets and who may need more marketing material sent to them about our widgets," George replied.

"While direct access to a production database is an entirely separate issue," Emmett began, "this event-driven data model would still allow for that type of analysis and so much more. No longer do you have to remain fixed on the present. You can now traverse various periods of time, gathering data such as at what time customers purchased widget A over cog B, correlate that with their location, and target your marketing to very specific groups of existing or potential customers to make your group much more effective. With this data we can begin incorporating more "big data" technologies to help us with this analysis and hopefully get things to a point where you no longer need to worry about access to the database anymore."

This made Verne from our security team give a slow clap and with that the hour was up and the meeting was finished.

## Wrapping Up ##
I hope you enjoyed this highly fictional tale about a highly factual data model. It really is a departure from the standard "one record to rule them all" mentality, but as my colleague Emmett said when he closed the meeting answering Buford's question on what will happen to mutability, "Mutability? Where we're going we don't need mutability."