# Pages and navigation

In this tutorial we will create an online cookbook. To achieve this, we have to:

1. Define a homepage;
2. Define a recipe page;
3. Navigate from page to page;
4. Add parameters to the recipe page.

## Setup

We start this tutorial with a project named `cookbook`. To learn how to set up a WebDSL project, follow our [Hello World tutorial](../hello-world).

## Defining the Homepage

Our homepage will list all recipes available in the cookbook. Open `cookbook.app` and change the root page accordingly.

```linenums="1"
application cookbook

  page root() {
    header { "Recipes" }

    list {
      listitem { "Lasagne" }
      listitem { "Pancakes" }
      listitem { "Tomato Soup" }
    }
  }
```

???+ info "Built-in templates"
    In the code snippet above, `header`, `list` and `listitem` are templates defined in the `built-in.app`. `header { "Recipes" }` is syntactic sugar for `<h1> "Recipes" </h1>`.

Once built and ran, the result (visible at `http://localhost:8080/cookbook/`) is a homepage with the static elements we defined.

## Adding a Recipe Page

Next, we would like to have an additional page in our application:

```linenums="1"
application cookbook

  page root() {
    header { "Recipes" }

    list {
      listitem { "Lasagne" }
      listitem { "Pancakes" }
      listitem { "Tomato Soup" }
    }
  }

  page recipe() {
    header { "I'm a recipe!" }
  }
```

In this updated version, visiting `http://localhost:8080/cookbook/` will still show the home page as we have seen before. The new recipe page is available on `http://localhost:8080/cookbook/recipe`.

## Adding Navigation

In our previous build, we have two separate pages but no link from one to the other. To add links to other pages, we can use the `navigate` construct that WebDSL provides:

```linenums="1"
application cookbook

  page root() {
    header { "Recipes" }

    list {
      listitem {
        navigate recipe() { "Lasagne" }
      }
      listitem {
        navigate recipe() { "Pancakes" }
      }
      listitem {
        navigate recipe() { "Tomato Soup" }
      }
    }
  }

  page recipe() {
    navigate root() { "Back to homepage" }

    header { "I'm a recipe!" }
  }
```

A `navigate` call consists of:

- A page call (i.e. `recipe()` in our case)
- The elements that the link should be on, between brackets (i.e. a single String `"Lasagne"` in our case)

The updated version of our application shows the list items as links to the recipe page, which now also includes a link back to the homepage.

## Add Page Parameters

The last thing to do, in order to complete our cookbook, is to show which recipe you are currently viewing. In the previous build, we could link from our homepage to the recipe page, but the recipe page was always the same.

To make the recipe page aware of the current recipe, we will add a page parameter to its definition, and update the page calls to the recipe page accordingly.

```linenums="1"
application cookbook

  page root() {
    header { "Recipes" }

    list {
      listitem {
        navigate recipe("Lasagne") { "Lasagne" }
      }
      listitem {
        navigate recipe("Pancakes") { "Pancakes" }
      }
      listitem {
        navigate recipe("Tomato Soup") { "Tomato Soup" }
      }
    }
  }

  page recipe(name : String) {
    navigate root() { "Back to homepage" }

    header { "Recipe: ~name" }
  }
```

???+ info "String interpolation"
    In the code snippet above, String interpolation (with a tilde `~`) is used to insert the parameter `name` in the header: `"Recipe: ~name"`.

The updated version of our application passes the recipe name as page pararmeter. This change is reflected in the URL. Instead of `http://localhost:8080/cookbook/recipe`, the `name` parameter is now a part of the new URL: `http://localhost:8080/cookbook/recipe/<name>`.

## Summary

In this tutorial, we created an online cookbook using multiple page definitions, page parameters and navigation between pages.
