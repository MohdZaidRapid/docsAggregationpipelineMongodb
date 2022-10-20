Aggregation pipeline 

###Match : Works like find in mongodb#####

db.aggreagte([{
$Match:condition}])
// for example
db.schemaName.aggregate([{
match:{tags:{$size:3}}
}])

##### Group in aggreagation ##########

#####group input documents by certain condition

## for example
db.schemaName.aggregate([{
group:{_id:"age"}}
])

#######It will output whole age of the person not whole documents
#group by nested fields

#$Match:condition  for example 


db.schemaName.aggregate([{
group:{_id:"$company.location.country"}

db.schemaName.aggregate([{_id:{"gender","age"}])

###### Combine Group and match  for example

db.schemaName.aggregate([{
$match:{gender:"female"}},
$group:{_id:{eyeColor:"$eyeColor",age:"$age"}}
}


])
	######It will output all documents with gender female (with all eyecolor and age)

####Swap orders


db.schemaName.aggregate([{
$group:{_id:{eyeColor:"$eyeColor",age:"$age"}},
$match:{gender:"female"}},
])

output no documents becuase no document



######### If documents are presents

db.schemaName.aggregate([{
$group:{_id:{eyeColor:"$eyeColor",age:"$age"}},
$match:{"_id.age":{$gt:30}}},
])


# #####
# result will be --->
# "_id":{
#     "eyeColor":"blue",
#     "age":27
# }

############## Count 

{$Count :"field"}

# output ====> {"field":4}

db.person.aggregate([
    $count:"allDocumentCount"
])


# output
{
    "allDocumentCount":1000
} 


#### different count methods
db.person.aggreagte([]).toArray().length
# time taken for 1000 documents  
# 1,7 sec --->1000

db.person.aggregate([]).itcount()
# time taken for 1000 documents 
# 1,4 sec ----->1000


# above two methods  are client side methods


# this is server side methods
db.person.aggreagte([{$count:"total"}]) and find.count will take same time
# 0,21 sec --->{"total":1000}


# Count  group

# match must be added before group stage
#group must be added before count stage
db.persons.aggregate([
    {$group:{_id:"$company.location.country"}},
    {count:"countriesCount"}
]) 
{"countriesCount":4}



# Sort stage 
# sort stage after match and group stage 
# 1 ascending
# -1 descending
{sort:{<field>:<1|-1>,<field:<1|-1>}}

db.person.aggregate([
    {$sort:{name:1}}
])


# Group and sort
db.person.aggreagte([
    {$group:{_id:{favorotefruits:"$favoriteFruit",eyeColor:"$eyeColor"}}},
    {$sort:{"_id.eyeColor":1,"_id.favoriteFruit":-1}}
])


# ne is equal to not equal to 



############################ Project ####################################

# syntax 
0-->Excluded
1-->Included
default :all fields 
{$Project:{<field>:<1>,<field2>:<0>,<newField1>:<>expression}}

example {$project:{name:1,"company.title":1}}
{$project:{_id:0,name:1,age:1}}


# Example 12: $project

db.persons.aggregate([
    {$project:{name:1,"company.location.country":1}}
])
# output ----> name location 


# Limit 
{$limit:<number>}

db.person.aggreagate([
    {$limit:100}.
    {$match:{age:{$gt:27}}},
    {$group:{_id:"$company.location.country"}}
])


# unwind
# input syntax {$unwind:,<arrayRefrencesExpression>} 

# Example  input array of fields
# {unwind:"$tags"}
# {unwind:"$hobbies"}

# output will be individual array field in array with whole documents  

# example 
db.person.aggregate([
    {unwind:"$tags"},
    {$project:{name:1,gender:1,tags:1}}
])
# output ---> individual array element



# special accumulator operator 
# $Sum $avg $min $max
db.person.aggregate([
    {
        $group:{
            _id:"$age",
            count:{$sum:1}
        }
    }
])
# array with unwind

# $sum 
db.person.aggregate([
    {
        {$unwind:"$tags"},
       { $group:{
            _id:"$age",
            count:{$sum:1}
        }}
    }
])



# Avg 
db.person.aggreagate([
    {
       
       { $group:{
            _id:"$age",
            avgAvg:{$avg:"$age"}
        }}
    }
])

# unary operator
# $type $or $lt $gt $and $multiply
# usually work with project stage

