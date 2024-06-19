# very-best-debug

Target: https://very-best-debug.matchthetarget.com/

Lesson: https://learn.firstdraft.com/lessons/155-very-best-debug

Debug: https://grades.firstdraft.com/resources/584b4f33-3574-4054-acc0-b07c098eb020

<hr>

Notes:

1. Added data using the command `rake sample_data`.
2. Review web app using the command `bin/dev`.
3. The action home is not found. The target shows that the home page is the users route. So, set :action=>"home" to :action=>"users". Keep the :controller=> "users" the same.
4. Missing template: change render({ :template => "users_templates/all_users"}) to render({ :template => "user_templates/all_users"}).

Fix broken hyperlinks.

6. Link to /users is broken: replace get("/users", { :controller => "users", :action => "all_users" }) to   get("/users", { :controller => "users", :action => "index" }). 
7. Fix typo to make sure variables are passed correctly from the controller to the html.erb page as @venue. 
8. Fix more broken links for show details on both the users and venues page.
9. Change get("/venues/:an_id", { :controller => "venue", :action => "show" }) to get("/venues/:an_id", { :controller => "venues", :action => "show" }).
10. Change venue_id = params.fetch("venue_id") to "an_id:". Determine this hash key by typing params in the rails console on the error page.
11. Trace error in the venue_templates in the details.html.erb by looking at venue_controller. In the class, the_venue should be @the_venue. Also, for matching_venues = Venue.where({ :id => venue_id }) you must add matching_venues = Venue.where({ :id => venue_id }).first. 
12. **Cool!** No method error at venues/6, undefined method "username". This error has to do with the comments class which needs to be set a foreign id to be linked to the users table. By setting a foreign id of venues, the venues table objects has a direct access to the comments table columns as added methods. You need to perform a join table to connect the author_id to the comment between the user and the comment tables. Replace <%= comment.commenter.username %> to <%= User.where(:id=>comment.author_id)[0].username %>. 
13. Fix users show details link. Routing error at users/sharilyn: unitialized constant UserController. Let's first look at the routes.rb: get("/users/:username", { :controller => "user", :action => "show" }). The controller should be "users"/
14. Next: Fix "Add user" button. Routing error: no route match (POST) - insert_user_record. 
```
post("/insert_user_record", { :controller => "venues", :action => "create" })
```

It turns out that the method is not defined. Change <form action="/insert_user_record" method=""> to <form action="/insert_user_record" method="post">. 

Subsequently, I got an error: Routing error: no route match (GET) - insert_user_record. Need to further modify the routes.rb script from   post("/insert_user_record", { :controller => "venues", :action => "create" }) to   post("/insert_user_record", { :controller => "users", :action => "create" }). 

**TIPS:** If the correction looks good, but the error still shows up, just restart the server and review. This final step usually resolves the issue.
15. Next: Fix "Add venue" button. Routing error: no route matches "/insert_venue_record". Looking at the html.erb page, I find the same issue as the "Add user" button. It is missing the "post" method. Change <form action="/insert_venue_record"> to <form action="/insert_venue_record" method="post">. Then, retsart the server to test.

Next error: undefined local variable or method venue. Need to fix the VenuesController class. The variable @venue should be consistently set as venue. Next, I see the error, no route matches GET /insert_venue_record. It is time to restart the server.

Next error: params missing. Use the debug rails console and query the key-value pairs for params. I found that the keys are not retrieved correctly. The keys are missing the prefixed "query". Upon refreshing the page, I got the error, no route matches GET /insert_venue_record. It is time to restart the server. 

Next: Look at the venue controller to see how this varable is defined. In the controller, a venue object is created but it is set as @venue whereas the associated attributes are set as venue.attribute. Change @venue to venue. Restart server. 

Next, look at the show method of the venue class to see how the parameters are passed on. This is associated with the dynamic route. I saw the route is directed to the dynamic route username, rather than to the venue id. Fix this within the redirect_to input function within the venues_controller.

In the following, change .name within redirect_to("/venues/#{venue.name}") to .id.
```
  def create
    venue = Venue.new
    venue.address = params.fetch("query_address")
    venue.name = params.fetch("query_name")
    venue.neighborhood = params.fetch("query_neighborhood")
    venue.save

    redirect_to("/venues/#{venue.name}")
  end
```

16. Next: Fix "Add comment" button.
