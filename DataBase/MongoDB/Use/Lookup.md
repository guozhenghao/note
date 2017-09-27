# MongoDB的连表查询
*连表查询方法在MongoDB 3.4版本以后才有*

- 使用方法
````
{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}
````
该方法也是在aggregate中使用