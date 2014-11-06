Association done with code
    @user = User.find_by_email("aditya1")

    @profile = Profile.new(profile_params)
    @user.profile = @profile







Let’s get inside a console session (rails console) and see how this comes together. If you have a console session
opened, run the reload! command in the console session to make sure it loads the newly generated models. Follow
along to create objects and relate them to one another. First, create a user and a profile as follows:
 
>> reload!
Reloading...
>> user = User.create(:email => 'user@example.com', :password => 'secret')
=> #<User id: 1, email: "user@example.com", password: "secret",
created_at: "2013-04-02 15:10:07", updated_at: "2013-04-02 15:10:07">
>> profile = Profile.create(:name => 'John Doe',
:bio => 'Ruby developer trying to learn Rails')
=> #<Profile id: 1, user_id: nil, name: "John Doe", birthday: nil,
bio: "Ruby developer trying to learn Rails", color: nil, twitter: nil,
created_at: "2013-04-02 15:10:55", updated_at: "2013-04-02 15:10:55"> 
■ ■ Note  The reload! method reloads the Rails application environment within your console session. You need to call it
when you make changes to existing code. It’s exactly as if you had restarted your console session—all the variables you
may have instantiated are lost.
Although you’ve successfully created a new user and a new profile, you haven’t yet associated them with each
other. If you ask the user object for its profile, it responds with nil:
 
>> user.profile
=> nil
 
To make the association happen, you specify it like any regular assignment on the user object and then call save,
like this:
 
>> user.profile = profile
=> #<Profile id: 1, user_id: 1, name: "John Doe", birthday: nil,
bio: "Ruby developer trying to learn Rails", color: nil, twitter: nil,
created_at: "2013-04-02 15:10:55", updated_at: "2013-04-02 15:10:55">
>> user.save
=> true
 
Assignment is assignment, whether it’s a name attribute to which you’re assigning the value Joe or an association
method to which you’re assigning an object. Also notice that the profile’s user_id attribute is updated to the value
of user.id: This is what bonds both objects together. Now, when you ask the user object for its profile, it happily
responds with one:
 
>> user.profile
=> #<Profile id: 1, user_id: 1, name: "John Doe", birthday: nil,
bio: "Ruby developer trying to learn Rails", color: nil, twitter: nil,
created_at: "2013-04-02 15:10:55", updated_at: "2013-04-02 15:10:55">
 
88
www.it-ebooks.infoChapter 6 ■ Advanced Active Record: Enhancing Your Models
That’s all there is to it. Although this is pretty good, you can do a bit better. You can create and save the profile in
one shot and have it perform the association automatically, like this:
 
>> user.create_profile :name => 'Jane Doe', :color => 'pink'
=> #<Profile id: 2, user_id: 1, name: "Jane Doe", birthday: nil,
bio: nil, color: "pink", twitter: nil, created_at: "2013-04-02 15:18:57",
updated_at: "2013-04-02 15:18:57">
 
Using the create_profile method to create a new profile initializes the Profile object, sets its foreign key to
user.id, and saves it to the database; at the same time, it sets the previous profile object’s—the one named “John
Doe”—user_id—field to nil. This works for any has_one association, no matter what it’s named. Active Record
automatically generates the create_#{association_name} method for you. So if you had an Employee model set up
with an association like has_one :address, you would get the create_address method automatically.
These alternatives for doing the same thing may seem confusing, but they’re really variations on the same theme.
In all cases, you’re creating two objects (the parent and the child) and telling each about the other. Whether you
choose to do this in a multistep operation or all on one line is entirely up to you.
Earlier, you learned that declaring a has_one association causes Active Record to automatically add a suite of
methods to make working with the association easier. Table 6-2 shows a summary of the methods that are added
when you declare a has_one and belongs_to relationship between User and Profile, where user is a User instance.
Table 6-2.  Methods Added by the has_one Association in the User/Profile Example
Method Description
user.profile Returns the associated (Profile) object; nil is returned if none
is found
user.profile=(profile) Assigns the associated (Profile) object, extracts the primary key, and
sets it as the foreign key
user.profile.nil? Returns true if there is no associated Profile object
user.build_profile(attributes={}) Returns a new Profile object that has been instantiated with attributes
and linked to user through a foreign key but hasn’t yet been saved
user.create_profile(attributes={}) Returns a new Profile object that has been instantiated with
attributes and linked to user through a foreign key and that has already
been saved