# Open Bracket Format (OBF)
### An open standad for tournament bracket data

Open Bracket Format (OBF) is a JSON schema for structuring tournament data. It is designed to be as flexible as possible across multiple sports or genres of games, and backwards-compatible as it sees updates. It is also designed to be able to reconstruct brackets. There are three main parts to the schema:

```py
{
    "event": {...},
    "entrants": [...],
    "sets": [...]
}
```


#### EVENT
This area carries information about the tournament itself.

Suggested optional fields to support:
- date
- game
- ruleset
- originURL
- numberEntrants

```js
{
    "event": {
        "name": "Jersey Japes",
        // Optional
        "date": "2021-04-20"
    }
}
```

#### ENTRANTS
Each entrant represents one spot in your bracket, and can be either an individual or team. They must have unique identifiers

Suggested optional fields to support:
- entrantTag
- initialSeed
- finalPlacement


```js
{
    "entrantID": "324245",
    // Optional
    "entrantTag": "CoolPlayer",
    "initialSeed": 2,
    "finalPlacement": 1
}
```

#### SETS
A tournament consists of multiple sets. Each set references two entrants that compete, with various rulesets that can determine the outcome. Each set must have a unique identifier. Sets are composed of rounds, which can include information such as character and stage.

Suggested optional fields to support:
- rounds (array)
- entrant1Result
- entrant2Result


```js
{
    "setID": "24601",
    "entrant1ID": "324245",
    "entrant2ID": "867530",
    // Optional
    "entrant1Result": "win",
    "entrant2Result": "lose",
    "rounds": [...]
}
```

### FAQ

#### What should I do if I don't know the value of some field (e.g. player character)?

Almost all fields in OBF are optional! If you don't have the information to fill in a field, simply omit that field from your final JSON object. 

The only required fields in OBF are the root-level `sets`, `entrants`, and `event` fields, the `name` field in `event`, the `setID` field for each set, and the `entrantID` field for each entrant. Aside from these fields, all other fields are completely optional. You can take a look at `examples/minimal.json` for an example OBF file which records relatively minimal information about a tournament (simply a collection of sets and their results), although even in this example many of the fields included are optional.

#### I want to store some information in an OBF file, but there's no field for it. What should I do?

Every object in OBF has an `other` field for this purpose. For example, if you would like to store the value of the pot bonus for the current event, you can do that by adding a `pot_bonus` field to the `other` object in `event` as follows:

```js
{
    "event": {
        "name": "Tournament Tournament",
        "other": {
            "pot_bonus": 1000.0
        }
    }
}
```

If you think there is a field that the current OBF specification is missing, you can suggest adding it to the specification by creating a GitHub issue in this repository (or by contacting the creators directly). 

PS: The specification only allows for adding new fields to the `other` subobject, not directly to any other object (e.g., it would be incorrect to add the `pot_bonus` field above directly to the `event` object). This is mostly to help us keep things forwards- and backwards-compatible as we add new fields to the current spec.

#### How can I check whether I've correctly formatted a tournament in Open Bracket Format?

There exist a number of free services online for checking whether a JSON file conforms to a JSON schema. One such service is [jsonschemavalidator.net](https://www.jsonschemavalidator.net/). By copying the contents of `tournamentSchema.json` into the left half of the page, and the contents of your JSON file into the right half of the page, you can check whether your JSON file breaks any of the rules defined by the schema. 

One warning: there are some rules we describe in the schema that cannot be captured in the JSON schema language, and therefore cannot be validated by these services. In particular, the rules that `setID`s and `entrantID`s must be unique across the `sets` and `entrants` array is not enforced by the schema, and will not be checked by these services. In the future, we hope to add a custom validation script to this repository that can check these additional constraints in addition to those imposed by the JSON schema.

#### How can I reconstruct the bracket from the information in an OBF file?

If the creator of the OBF file has populated the `winnerNextSetID` and `loserNextSetID` fields for each set, you can use those to reconstruct the bracket. The creator also has the option to populate the `entrant1PrevSetID` and `entrant2PrevSetID` fields, which can further help with reconstruction. In the future, we plan to add an example script demonstrating how to reconstruct single-elimination and double-elimination brackets from an OBF file.

#### Do you have any examples of tournaments in Open Bracket Format?

This repository currently has two example tournament OBF files in the `examples/` folder. 

#### What can I do to help out?

Many things! The most valuable thing you can do is try to use this format in your own projects (e.g. if you write software for managing tournaments, adding an option to export tournaments in OBF format). If there is anything preventing you from using the current OBF specification, please let us know!

Secondly, there are a number of ways to contribute to this project technically. In particular, we would like to eventually include in this repository:

- a validation script for the OBF format.
- a script for reconstructing single-elim and double-elim brackets from OBF files.
- scrapers for converting common web tournament formats (e.g. challonge.com, smash.gg) into OBF.

If you are interested in writing any of these, please let us know (or just directly send a pull request). 

Finally, this project is still in its early stages, and we're still looking for input. If you have any suggestions on how we can change this spec for the better, please file a GitHub Issue or contact one of the creators. Thanks!