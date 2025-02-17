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

# 3 Creating database schema 

what table are we need ? 
1.post
2.category
3.category_post
4.likes
5.comments
6.roles

first we are going to create first 3 -> 

for Post -> Model, Migrations, Controller, Factory
for category -> Model , Migration, Factory 
for category_post -> migration only.. 

$table->foreignIdFor(User::class); -> if we give foreignId (foreignIdFor) like this laravel automatically understands User class , we dont need to define column name.

fill the tables and add some fake data in for factory then try to access it for in tinker , that's it .. 

# 4 working on homepage

use the app-layout as home page layout 

copy the home page code from static github html code .. (from that github)

make some cleanups in home.blade 

then make a controller for HomePage 
> php artisan make:controller HomeController -i

why we are creating invoke controller ? becoz we are going to do only one task, that's why 

goto HomeController , return the home page view

    public function __invoke(Request $request)
    {
        return view('home');
    }
then change the route..

    Route::get('/', HomeController::class);

try to pass some data from HomeController to home.blade file (for education purpose)

then we are going to create a new component
> $ php artisan make:component Posts/PostCard --view

why we are giving view flag ? becoz usually if we didnt give view flag laravel creates Class and View file , but we need only view file that's why we give flag (view)

then cut the post card related things pasted in it .. then replace it with a <x-posts.post-card />

then we are going to pass the props in it .. 

```bladehtml
    @foreach($featuredPosts as $post)
        <div class="md:col-span-1 col-span-3">
            <x-posts.post-card :post="$post" />
        </div>
    @endforeach
```
just like this .. 

then add props in post-card to

    @props(['post'])

that's it... 

do that same for Latest Posts 

we can do more dynamic (original data)

    public function __invoke(Request $request)
    {
        return view('home', [
        'featuredPosts' => Post::where('published_at', '<=', Carbon::now())->take(3)->get(),

instead of this big write queries in controller we can do that in scope(In model)

    public function scopePublished($query){
        $query->where('published_at', '<=', Carbon::now());
    }
just like this.. 
and back to Controller add that published() [skip that (scope) prefix]

    'featuredPosts' => Post::published()->take(3)->get(),
just like this .. it works perfectly as it before .. 

we can do same like this for featured too.. (scope)

    public function scopeFeatured($query){
        $query->where('featured', true);
    }
then go to controller .. 

    'featuredPosts' => Post::published()->featured()->take(3)->get(),
that's it.. 

then we are going to do exact same thing what we did in featuredPosts -> latestPosts

    'featuredPosts' => Post::published()->featured()->latest('published_at')->take(3)->get(),
    'latestPosts' => Post::published()->latest('published_at')->get(), #[Note this]

then we do change in logo .. 

# 5 working on Blog Page Stub

we are making some changes in profile 

then after we logged we need to redirect home page so , 
goto -> config -> fortify -> there is section home change its value
just like this -> 'home' => '/',

then we are going to create a blog(posts)
first create a route for it ..
 - Route::get('/blog', PostController::class, 'index')->name('posts.index'); //posts

then create that posts.index file 

then create that index action (we defined in controller) on PostController

    public function index(){
        return view('posts.index');
    }

then go to that github html code page copy the main content in blog.html .. 
paste that in posts.index (inside of the x-layout tag)

then create a component for storing post-item
-  php artisan make:component Posts/PostItem --view

then cut the whole article tag and paste in it .. 

then replace this <x-posts.post-item /> in index file 

for we can pass this data from controller to index blade 

    public function index(){
        return view('posts.index', [
            'posts' => Post::take(5)->get()
        ]);
    }
then loop over in index file

    @foreach($posts as $post)
        <x-posts.post-item :post="$post" />
    @endforeach

then pass the props in post-item 

that's it works fine .. 

then make a changes in post-item , make it dynamic .

then we are counting the readingTime 

goto -> post model 

    public function getReadingTime(){
        $mins = round(str_word_count($this->body)/ 250);
        return ($mins < 1) ? 1 : $mins;
    }
then apply this in post-item .. 

that's it .. 

# 6 
 Today we are going to create our first livewire component for Post List 

> php artisan make:livewire PostList

    #[Computed()]
    public function posts(){
        return Post::take(5)->get();
    }

ipdi Computed Attribute use pannum pothu, ovoru  public property um assign pannanum nu avasiyam illa .. 

then 
    blade file la itha epdi call pannanum naa.. 

        @foreach($this->posts as $post)
            <x-posts.post-item :post="$post" />
        @endforeach
intha mari $this-> kuduthu call pannanum.. 

next we are creating livewire component for Search 

SearchBox

cut the search code from index.blade and pasted in .. change make sure to give <livewire:search-box /> in index.blade

in that Searchbox livewire component 

public $search = "";

    public function updatedSearch(){
        $this->dispatch('search', search: $this->search);
    }

write this .. (this is hook method)

write this in

wire:model="search"
class="w-40 ml-1 bg-transparent focus:outline-none focus:border-none focus:ring-0 outline-none border-none text-xs text-gray-800 placeholder:text-gray-400"
type="text" placeholder="Search...">

Search box blade file's input .. we are writing the search using hook so prefix Updated.. 

then get the call from search box to Postlist .. becoz we want to search in Postlist so go to postlist .. 

#[Url()]
public $search = "";        -> this will get the url after we search something .. 

#[On('search')]
public function updateSearch($search){
$this->search = $search;

then write that search filter in posts function 

#[Computed()]
public function posts()
{
...
->where('title', 'like', "%{$this->search}%")
....
}

that's it now try to search , its working .. 
