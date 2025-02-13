# Introduction

- install jetstream

then go to config -> jetstream -> features -> 
enable -> termsAndPrivacyPolicy and profile pictures 
disable -> accountDeletion 

# 2 

lets take a model tailwind css code from github .. (as per his instruction)

then paste it in app.blade layout file and do some modification .. 

then create a folder in layouts called 'partials'
inside of this partials we are going to create a two blade file 
footer and header 

cut the footer code in app layout and paste it in footer.blade.php 
cut the header code in app layout and paste in header.blade.php

then replace it with a  @include('layouts.partials.footer') , @include('layouts.partials.header') in app.layout 

then give this {{ $slot }} place holder in main section 

then we need a profile dropdown on our dashboard , so go to navigation-menu.blade.php 
and copy the Settings Dropdown code and paste that in our header (partials->header.blade.php)

1)
after we getting logged in still shows that Login and Register links .. 
after login we did not show that .. so go to header.blade file -> go to the Login and Register link section 
give them into a @guest - @endguest , so we can't see a login and Register links after logged in .. only guests can see it 

2)
then settings dropdown only can auth user can should be see that .. so give them into @auth - @endauth 

instead of doing this create a new two blade files in layout->partials 
header-right-auth.blade 
header-right-guest.blade 

put those codes into this..

    @guest
        @include('layouts.partials.header-right-guest')
    @endguest

    @auth
    <!-- Settings Dropdown -->
        @include('layouts.partials.header-right-auth')
    @endauth

now its working as before .. 

