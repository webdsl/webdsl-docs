# Recommend

Collaborative filtering recommendations are supported in the WebDSL language using the [Mahout framework](http://mahout.apache.org/).

## General idea
Collaborative filtering systems are content agnostic. These systems focus on the relationships between users and items and not directly on the content properties of their corresponding entities. From here onwards we refer to users and items as general entity types that will be related, this is further discussed below.

## Examples
One example is a book store where consumers buy books, so in this case the consumer is the user and the book is the item. Users have preferences for items, but its up to you whether you take those into account. The preference value can be expressed on a scale that matches your needs, for example 0 to 1 or 1 to 5, as long as it’s a number.

```
entity Customer {
	name : String(id)	
	books : List<BookPurchase>
}

entity Book {
	isbn : String(id)
	author : String
	title : String
}

entity BookPurchase {
	by : Customer
	book : Book
	pref : Int
}

recommend BookPurchase {
	user = by
	item = book
	value = pref
}
```

For Mahout these preferences indicate which books are very popular and which are less. Such preferences are not obligatory but if implemented can be used to order the recommendations based on popularity.  

Another example are friend relations where you can recommend users to users based on their current network of users. In this case the item is also the user type. The opposite holds as well. You could have books that recommend other books. This is useful if you do not know the users preferences, for example with an unidentified visitor. In this case you talk about item to item relations. These relations change less often and therefore require less calculations for Mahout on a regular basis.  

Users need to have items in common in order for there to be recommendations. Otherwise in statistical terms the precision of the relationships can not be calculated. Based on solely the link between users and items you could already express preferences. If you don’t know the user, it is still possible to obtain recommendations based on the current item that is shown. In this case it is an item to item relation. Otherwise if you do know the user and he or she has one or more relations to items, you could generate a list of recommendations based on his or her preferences.  


## Configuration
In order to express the relation between a user and his or her items you need to have a special entity. This entity holds both the original user and the item and might have a preference value. As a developer you could add other information too, for example the date when the relation was created. A list of relation items is stored in the user entity, as shown in the book example above. If the preference value is not added to the relation entity, the recommendation system automatically expects a boolean relation.

### Recommendation block
The name of the relation entity is used as the name to call the recommendation system.
```
recommend RelationEntityName {
	user = by
	item = book
	value = pref
	algorithm = Loglikelihood
	neighborhoodalg = NUser
	neighborhoodsize = 9
	type = Both
	schedule = 1 weeks
}
```
The recommendation block has two obligatory values. These specify which variable in the relation entity holds the reference to the user and the item instance. So in above’s book store example the name of the recommendation block matches the name of the relation, which is BookPurchase. The user and item variables are set to by and book respectively.
The user and item values:

**user:**
The variable name that references the User instance
inside the relation entity.  

**item:**
The variable name that references the Item instance
inside the relation entity.  

The other optional values are listed here:  


**algorithm:**
Can be of value: Euclidean, Pearson, Loglikelihood,
or Tanimoto. This specifies which algorithm the Mahout framework
should use in order to build up a list of recommendations.
The default value is: Loglikelihood. Please be aware that the
precision of the recommendation is largely determined by the
algorithm that is chosen, go to the website of the [Mahout framework](http://mahout.apache.org/)
to read more about the benefits and drawbacks of these algorithms.
*Note:* In case you want to test the precision of your choice and
determine the related speed have a look at the test procedures
discussed further below.  

**neighborhood size:**
Can be any non-null numeric value. 
This specifies the size of the neighborhood that Mahout needs
to look into find other recommended items.   
The default value is: 9.  

**neighborhood algorithm:**
Can be of value: NUser or Threshold.
This specifies which algorithm should be used to determine the
neighborhood in the dataset. This can have a major effect on
the precision of the recommendations returned by the
recommendation system.   
The default value is: NUser  

**type:**
Can be of value: User, Item, or Both.
This specifies whether you want to use the recommendations
for User-to-Item relation, Item-to-Item relation, or even both.
It is highly recommended to set the flag on the most limited form
that you need, this will drastically decrease the amount of time
required to compute the recommendations.  
The default value is: Both  

**schedule:**
This can be any time interval value as defined
fully at [Recurring Tasks](https://webdsl.org/selectpage/Manual/RecurringTasks).
This defines at what interval the recommendation system should
rebuild the mahout index in the background.  
*NOTE:* Do not set this value less than the time it would take to compute the index once.  
The default value is: 1 weeks  
  
**NOTE on scheduling** Scheduling through the definition of the recommendation is not supported yet, instead use the default method of WebDSL to schedule the reconstruct function every x days. For example:
```
invoke RelationEntity.reconstructRecommendationCache() every 2 days
```


## Implementation
After configuring the recommendation system you can implement it in the code using the name of the relation entity. In the above mentioned book store example this is the BookPurchase entity name. All the recommendation functions for this relation are accessible through the corresponding relation entity as if it were a static function of that entity class.  
 
There are two ways to get recommendations, which method you need depends on the situation you are in. In the most common situation you know who the user is, and you want to get recommendations based on the items he / she already likes. In this case we talk about the user-to-item recommendations. In case you do not know the user, but you know which item they are interested in you could generate a list of recommendations too. The latter case is called item-to-item recommendation. These two methods require a different function call to the Recommendations system.  

### User-to-item recommendations
The user-to-item recommendations are accessible by calling to the method:
```
RelationEntity.getUserRecommendations(user : UserType, maxNum : Int) : List<ItemType>
RelationEntity.getUserRecommendations(user : UserType) : List<ItemType>
```
This method returns a `List<ItemType>` as its result and could be filtered further or simply looped through as any normal list. The user argument refers to the user for which you want to obtain recommendations. The maxNum argument is the maximum number of recommendations that you want to obtain, this argument is optional and by default set to 10.

### Item-to-item recommendations
The item-to-item recommendations are very similar to the user-to-user recommendation methods. The major difference between these function calls is the fact that you supply an item to the function so you could get a list of recommendations based on that single item. The item-to-item methods are:
```
RelationEntity.getItemRecommendations(item : ItemType, maxNum : Int) : List<ItemType>
RelationEntity.getItemRecommendations(item : ItemType) : List<ItemType>
```
Just like the user-to-item recommendations these functions result in a `List<ItemType>`. The item argument refers to the item that you want to use as a reference to obtain recommendations. The maxNum argument is the maximum number of recommendations that you want to obtain, this argument is optional and by default set to 10.

## Testing the speed
Determining the recommendations takes a while to compute, this is dependent on several factors including the size of the data set, the type of relations (binary or value based), if there are duplicate relations possible, and several configuration options. The configuration options that play a major role here are the algorithm type, the neighborhood size, and its related neighborhood algorithm. With the following function call you can determine how long the last operation of the recommendation system took in milliseconds:
```
RelationEntity.getLastExecutionTime() : Int
```

## Precision and recall testing
The precision and recall test is used to determine the precision of the recommendations. In other words, with this test you can determine the quality of the recommendations that will be given back. The precision and recall performance of the recommendation system depends on the algorithm used and the type of network you want it to analyze.  
  
*NOTE:* Do not use this function on production systems, as it takes a while to compute and does not add any value on a live production machine. It should only be used to determine the type of algorithm that performs best during the development phase.  
  
*REQUIREMENT:* In order for the precision and recall method to return valuable results you need to test a network that is filled with data as it would be when used in production. Compared to a real data set a random data set could falsely state that the performance of algorithm x is very good, while you should use algorithm y.
```
RelationEntity.evaluateIRStats() : String
```
The precision and recall float values are both encapsulated in a string returned by this function, for example: `"0.2714285714285714 :: 0.2857142852714785"`

## Building the mahout index
With the schedule parameter of the recommend configuration you can set when the mahout index should be rebuilt as discussed before. However, if you want to build the mahout index manually that is possible too.
The function you need to call in order to reconstruct the index is:  

*NOTE:* Do not use the manual function on production systems as it can take several hours to compute and would block all the open connections to the website in the meanwhile.  
```
RelationEntity.reconstructRecommendationCache() : void
```