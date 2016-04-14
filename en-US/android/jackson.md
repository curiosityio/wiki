---
name: Jackson
---

# Tips

* Map a specific JSON property to a VO property of a different name.

Lets say your JSON looks like:
```
{
    "id": 1,
    "username": "levi"
}
```
You want your VO to look like:
```
public class UserVo {

    public int user_id;
    public String username;
}
```
So you want the JSON property "id" to bind to "user_id" in the VO.

How. Add this to your VO:
```
public class UserVo {

    public int user_id;
    public String username;

    // map JSON response 'id' to user_id.
    @JsonProperty("id")
    public void setUserId(int user_id) {
        this.user_id = user_id;
    }
}
```

* Jackson ignore unmapped fields from JSON.

When you create a VO object and attempt to map properties from the JSON to a VO object, Jackson will throw an exception when it cannot find a field in the VO object for the property.

Lets say your JSON looks like:
```
{
    "id": 1,
    "username" = "levi"
}
```
You want your VO to ignore the username and just keep track of the id. So your VO looks like:
```
public class UserVo {

    public int user_id;
}
```
If you try mapping the JSON to this VO, Jackson will throw an exception. Add this to your VO object to prevent the exception from being thrown:
```
@JsonIgnoreProperties(ignoreUnknown = true)
public class UserVo {

    public int user_id;
}
```
