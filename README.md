# geminabox-sync
Ruby gem that accepts a Gemfile via a rake command and syncronizes the gems in your own geminabox

## Rake Tasks

Command | Information
--------|------------
`bundle exec rake geminabox-sync:install` | Should create a `.geminabox-sync.yml` file (see below)
`bundle exec rake geminabox-sync:sync` | Reads the `Gemfile.lock` and syncronizes the gem dependencies with geminabox
`bundle exec rake geminabox-sync:check` | Performs a check that geminabox has every gem contained in `Gemfile.lock`

## Gem-In-A-Box via `Docker`

    # If you do not have a data AND gems directory already...
    mkdir .{data,gems}
    touch {data,gems}/.git-keep
    echo "data" >> .gitignore
    echo "!data/.git-keep" >> .gitignore
    echo "gems" >> .gitignore
    echo "!gems/.git-keep" >> .gitignore
    git commit .gitignore -S -m 'Added data and gems directories'
  
    # If you have a data directory already
    docker pull lheinlen/geminabox
    docker run --name my-geminabox -v ./data:/opt/geminabox/data -d -p 9292:9292 lheinlen/geminabox
    docker ps | grep my-geminabox

## Installer

The `geminabox-sync:install` rake task should create a yaml file and prompt the user in the terminal for the appropriate pieces of information. 

> `.geminabox-sync.yml`
```yml
host: localhost:9292
```

## Sync Task

- Parse the entire `Gemfile.lock` and extract each gem and its exact version
- Run `git fetch <gem> -v <version>` against each of the gems listed in the `Gemfile.lock` and save them inside the `gems/` directory
- Push the gem via `git push <gem>-<version>.gem --host <config host>` to the host defined in the `.geminabox-sync.yml` file 
