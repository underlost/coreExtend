### CoreExtend

coreExtend is what I'd like to call, the missing django app. It includes many useful (at least to me) things I use with almost every web app I make. So, what's included?

First off, we have the AbstractUser model, Account. I like working with this just because it gives a little more freedom and easibility to include additional fields in the user model, while still retaining all the built in features Django's authentication has.

Next we have additional template tags, with values we can define in setting.py. I mainly like doing this to set a {{SITE_NAME}}, {{SITE_DESC}}, etc in templates, and defining it in settings, so it's easier to change later on if I want to edit, rather then going file by file to make the changes.

I typically also like to use [Django REST framework](http://www.django-rest-framework.org/) in my projects that require an API, so I also included are the basics for getting the current logged in user, profile details, user lists, and so on.

Sometimes you need to include subdomains in your app. Whether its to create a username.yourdomain.com address, or an api subdomain like api.yourdomain.com, you cant easily do this by default. Therefore I put together a middleware to process subdomain url requests a little easier.

And finally, templates and urls that hook into Django's views for logging in/out, password changes, and account recovery, along with a slightly improved registration form for cleaner usernames.


### Installing

It really depends what you want to use. I opted out of creating a setup script because coreExtend is meant to be more of a starting point, it should probably be included locally in your project to edit and expand upon, rather then to just include.

To get it working with your project, there's just a few things you need to change in settings.py:

1. Include coreExtend in the installed apps:

`INSTALLED_APPS = (
	...
    'coreExtend',
)`

2. Add it to the context processors:

`TEMPLATE_CONTEXT_PROCESSORS = {
    ...
	'coreExtend.context_processors.template_settings',
    'coreExtend.context_processors.template_times',
}`

Next define your site's settings, like so:

`#Site Settings
SITE_NAME = os.environ.get('SITE_NAME', 'A Life Well Played')
SITE_DESC =  os.environ.get('SITE_DESC', 'Video Game News Commentary')
SITE_URL =  os.environ.get('SITE_URL', 'https://alifewellplayed.com/')
SITE_AUTHOR = os.environ.get('SITE_AUTHOR', 'Tyler Rilling')`

to easily manage the site's configs, you can use os.environ.get() to expose it to, say, Heroku's config to make changes with out needing to edit the settings.py file directly.

3. Add the middleware for subdomains (sidenote: The coreExtend middleware should be added as early as possible in MIDDLEWARE_CLASSES.):

`MIDDLEWARE_CLASSES = (
    'coreExtend.middleware.SubdomainURLRoutingMiddleware',
	'coreExtend.middleware.MultipleProxyMiddleware',
    ...
)`

Then define your subdomains, if any, like so:

`SUBDOMAIN_URLCONFS = {
	None: 'wellplayed.urls',
    'api': 'wellplayed.apps.replica.api.urls',
}`

Even if you do not define any subdomains in SUBDOMAIN_URLCONFS, `None` should still be defined with the urls.py, usually the same as what's defined in ROOT_URLCONF, or whatever you wish to choose.

4. Add coreExtend to your urls.py:

`urlpatterns = patterns('',

	#admin
	url(r'^admin42/', include(admin.site.urls)),
	url(r'^', include('coreExtend.urls', namespace='Core')),
    ...
`
