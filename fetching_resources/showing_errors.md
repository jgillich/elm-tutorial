# Showing Errors

Our application fetches a list of users but it doesn't account for errors. Kill the fake API server to try this.

When you refresh you will see an empty list and no indication of the error that just happened.

The problem is in __src/Players/Update.elm__:

```elm
        Err error ->
          ( model.players, Effects.none )
```

This just throws the error away. We need some way to show the error.

## Flash messages outlet

As errors are a common thing that could happen across modules we want a shared outlet where to show messages in our main view.

Start by creating a new attribute in the main application model. In __src/Models.elm__ add `errorMessage`:

```elm
type alias AppModel =
  { players : List Player
  , routing : Routing.Model
  , errorMessage : String
  }


initialModel : AppModel
initialModel =
  { players = []
  , routing = Routing.initialModel
  , errorMessage = ""
  }
```

This could be more generic, for example having `flashMessage` and a `flashKind` so we can show errors, warnings and info messages. But for now we will just do errors.

Let's add the flash message outlet in our main view. In __src/View.elm__ add:


```elm

...
import Html.Attributes exposing (..)
import String
...


flash : Signal.Address Action -> AppModel -> Html
flash address model =
  if String.isEmpty model.errorMessage then
    span [] []
  else
    div
      [ class "bold center p2 mb2 white bg-red rounded"
      ]
      [ text model.errorMessage ]
```

And add the flash messages to the `view` function in the same file:

```elm
    ...
      [ flash address model
      , page address model
      ]
```

## Action to show the error

In __src/Actions.elm__ add a new action:

```elm
  ...
  | ShowError String
```

And update __src/Update.elm__ to include this action. Add a new branch to the case expresion:

```elm
    ...
    ShowError message ->
      ( { model | errorMessage = message }, Effects.none )
```

---

Try what we have so far by adding a hard coded error in __src/Models.elm__, add this to `initialModel`:

```
...
  , errorMessage = "Error"
```

Refresh and you should see this error message. Put it back to "".

We have half of the wiring for showing an error message. Now we need some way to trigger this `ShowError` action from Players/Update.


