# `bull` always charges...

[![Build Status](https://travis-ci.org/jeffknupp/bull.png?branch=develop)](https://travis-ci.org/jeffknupp/bull)
[![Coverage Status](https://coveralls.io/repos/jeffknupp/bull/badge.png?branch=develop)](https://coveralls.io/r/jeffknupp/bull?branch=develop)

## WTF is `bull`?

On a Friday evening, not too long ago, I was lamenting the shortcomings of the
various services I've used to sell my digital book from my personal website. 
*Digital goods sales should be a solved problem by now*, but I ran into all 
sorts of issues when trying to send updates to customers and integrate 
properly with Google Analytics.

[Long story](http://www.jeffknupp.com/blog/2014/01/18/python-and-flask-are-ridiculously-powerful/) short, I 
took two hours and wrote a replacement using Python, Flask, SQLAlchemy, and
Stripe (as the payment processor). `bull` is to set up *on your own domain*. Why 
does the fact that `bull` runs on your own domain matter? Because it
makes Google Analytics happy and report conversions properly.

The only things you need to get started using `bull` are a Stripe account (free)
and a web server (free?).

## Installation

1. `pip install bull` 
1. Create a file named `app.py` with the following contents: 

~~~~{.py}
    from bull import app, db
    def get_app():
        """Return the application object."""
        return app

    if __name__ == '__main__':
        app.config.from_object('config')
        with app.app_context():
            db.metadata.create_all(bind=db.engine)
        get_app().run(debug=True)
~~~~

1. Grab the file `config.py` either from the repo or the installation and enter
your own values
1. Create a directory named `files` and put your products' files in there
1. Add product entries to the database (use `scripts/populate_db` as a model)
1. (Optional) Create an admin user for viewing `/reports` by running `scripts/create_user.py`
1. Add `bull` to your web server's configuration 
1. Profit! (...literally)

## Analytics and Login

`bull` now supports simple sales analytics at the `/reports` endpoint. It
requires authorization, which in turn requires you to create (one) user using
the `scripts/create_user.py` script. To see the reports, hit `/login`, log
in, and from then on you can go directly to `/reports` to see reporting data. 
You should be good to go after that, and no one else will be able to see the
reports.

**Reporting includes:**

* Email addresses and sales totals of recent purchases
* Sales data broken down by calendar day
* Sales charts based on revenue/units sold per day

## Overriding Default Templates

Simply create a `templates` directory and create a file of the same name as the
template you want to replace.

## Testing

`bull` has a (small) suite of tests that are run via TravisCI, but can (and should)
also be tested manually once installed.  Run `python app.py` and browse to 
[http://localhost:5000/test/1](http://localhost:5000/test/1).
You should see a single "Buy" button, which should be completely functional
(assuming you have at least one product in your database).
Enter Stripe's test credit card number (4242 4242 4242 4242). You should be
successfully directed to a "success.html" page with your download link. If your
product is in the `files` directory, you'll be able to download it by clicking
the link.

## Deployment

*Don't run `app.py` in production.* The web server it uses is not meant for such
a purpose. Instead, deploy as you would normally deploy an WSGI application. See
[Flask's documentation](http://flask.pocoo.org/docs/deploying/) on the subject.

## TODO

Still need to add better documentation and (possibly) deployment information.
All of this is coming. I just wanted to get `bull` out as fast as possible 
so that those who know what they're doing can make use of it.
