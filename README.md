///------> Advance MongoDb Concepts & Queries <-------/// 

1. or 
    - User.find({ $or: [{name: 'test'}, {age: '20'}] })

2. and
    - User.find({name: 'test', email: 'test@gmail.com'})
    - User.find({ $and: [{name: 'test'}, {age: '20'}] })

3. less than
    - User.find({ age:{lt:'25'} })

4. less than equal
    - User.find({ age:{lte:'25'} })
    
5. greater than
    - User.find({ age:{gt:'25'} })
    
6. greater than equal
    - User.find({ age:{gte:'25'} })
    
7. like
    - User.find({email: /test/})

8. IN
    - User.find({ age:($in:['22', '25']) })

9. nor
    - User.find({ $nor: [{name: 'test'}, {age: '20'}] })

10. NOT
    - User.find({ $not: [{name: 'test'}, {age: '20'}] })

11. AND OR
    - User.find({ name: 'test', $or: [{age:'20'}, {email: 'test@gmail.com'}] })

------------------------------------------------------------------------------------------------

SELECT ONLY SPECIFIC FIELDS IN RESPONSE 

* Projection : User.find({email: "test@gmail.com"}, {name:1, email:1, password:0})

------------------------------------------------------------------------------------------------

MATCHING ARRAY VALUE INSIDE AN OBJECT

* $elemMatch : User.find({ 'social' : { $elemMatch: {name:'youtube'} }})

-----------------------------------------------------------------------------------------------

* DISTINCT & COUNT

    - User.distinct('department', {'status': 'active'});
    - User.find({'status': 'active'}).count();

-----------------------------------------------------------------------------------------------
* MONGODB AGGREGATE
-----------------------------------------------------------------------------------------------

    - $match : User.aggregate([{ $match: {'status':'active'} }])

    - $group : User.aggregate([{ $group: {_id: '$department', totMarks: {$sum: '$marks'}} }])
    
    - Multiple GroupBY : User.aggregate([{ $group: {_id: {age: '$age', gender : '$gender'}} }])
    
    - Skip & Sort : User.aggregate([{ $group: {_id: '$department'}}, {$skip:1}, {$sort:{_id: -1}}]) 
    
    - $project : User.aggregate([ {$match: {status: 'active'}}, {$project: {name:1, email:1, password:0}}])
  
    - alias : User.aggregate([ {$match: {status: 'active'}}, {$project: {fullName:'$name', emailId:'$email', password:0}}])

    - $type : User.aggregate([ {$match: {status: 'active'}}, {$project: {name:1, email:{type: '$email'}, password:0}}])

    - $count :  User.aggregate([ {$match:{status: 'active'}}, {$count: 'total'} ])

    - $limit : User.aggregate([{$match: {status: 'active'}}, {$limit:10}])

    - $unwind :  User.aggregate([{$unwind: "$language"}, {$match: {status: 'inactive'}}, {$project: {name:1, language: 1}}])

    - $match & $group : User.aggregate([
        {$match:{department: {$in : ['HR', 'TECH']}}},
        {$group: {_id: '$department', tot:{$sum: '$marks'}}},
        {$match: {tot: 170}}
    ])

    - $out : User.aggregate([{$match: {status: 'active'}}, {$project: {email:1, name:1, password:0}}, {'$out':'infoUser'}])

    - $lookup : User.aggregate({
        $lookup:{
            from: "department",
            localField: "dept",
            foreignField: "name",
            as: "anything" 
        }
    })


-----------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------

* Export Database : 
        - $ mongodump // Export all Database 
        - $ mongodump -d databaseName // Export Single Database 

* Export Collection : 
        - $ mongodump -d databaseName -c collectionName // Export Single Collection 

* Import Database 
        - mongorestore -d databaseName  -c admin user.bson


-----------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------

* PAGINATION & SORTING 

    http://localhost:8000?page=1&limit=10&sort=id&asc=-1

    app.get('/', (req, res) => {
        let {page, limit, sort, asc} = req.body

        if(!page) page = 1; 
        if(!limit) limit = 10; 

        const skip = (page - 1) * 10;

        const users = await User.find()
                    .sort({ [sort]: asc })
                    .skip(skip)
                    .limit(limit);
        
        return res.json({ page: page, limit: limit, users: users});
    })

-----------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------

