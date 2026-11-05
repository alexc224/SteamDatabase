# SteamDatabase
A database of games on steam that are found using the Steam API

## Purpose
I am building this database so that people can check the entire steam database of available games and dlcs and check their account networth. 
## Use Cases
Users should be able to look at the steam database which will be seperated between games and dlc with drop boxes so they can sort games and dlcs by different paramaters. They should also be able to search for games and dlcs.
## Business rules
1) For users that are signing up, they need exactly 1 unique username and 1 unique hashed password and a unique steam_id. Inside the database they
2) Games will be tied to a table with unique ids where they store name, price that doesn't go below zero and is in the format for usd, publisher and different types of ratings (total,total positive, total negative and rating score)
3) Dlcs will be the same as the games, having a single unique dlc_id and being tied to a game_id
4) for the libraries that are tied to users, it will need multiple user_id and game/dlc ids. The primary key will be a unique combination of the user_id and game/dlc id. This will be in two different tables (1 for games and 1 for dlc)
## Diagram
```erDiagram
    users {
        INTEGER user_id PK "GENERATED ALWAYS AS IDENTITY"
        BIGINT steam_id UK "NOT NULL"
        VARCHAR email UK "NOT NULL"
        VARCHAR password_hash "NOT NULL"
        DATE account_join_date "DEFAULT CURRENT_DATE"
    }

    games {
        INTEGER game_id PK "GENERATED ALWAYS AS IDENTITY"
        VARCHAR name "NOT NULL"
        NUMERIC price "CHECK >= 0"
        VARCHAR genre
        VARCHAR rating_desc
        INTEGER rating_total "DEFAULT 0"
        INTEGER rating_positive "DEFAULT 0"
        INTEGER rating_negative "DEFAULT 0"
        NUMERIC rating_pct "DEFAULT 0"
        VARCHAR publisher
        DATE release_year
    }

    dlc {
        INTEGER dlc_id PK "GENERATED ALWAYS AS IDENTITY"
        INTEGER game_id FK "REFERENCES games(game_id) ON DELETE CASCADE"
        VARCHAR name "NOT NULL"
        NUMERIC price "CHECK >= 0"
        DATE release_date
        VARCHAR publisher
        VARCHAR rating_desc
        INTEGER rating_total "DEFAULT 0"
        INTEGER rating_positive "DEFAULT 0"
        INTEGER rating_negative "DEFAULT 0"
        NUMERIC rating_pct "DEFAULT 0"
    }

    player_library {
        INTEGER user_id PK, FK "REFERENCES users(user_id) ON DELETE CASCADE"
        INTEGER game_id PK, FK "REFERENCES games(game_id) ON DELETE CASCADE"
        INTEGER game_hours "DEFAULT 0, CHECK >= 0"
    }

    dlc_library {
        INTEGER user_id PK, FK "REFERENCES users(user_id) ON DELETE CASCADE"
        INTEGER dlc_id PK, FK "REFERENCES dlc(dlc_id) ON DELETE CASCADE"
        INTEGER game_id FK "REFERENCES games(game_id) ON DELETE CASCADE"
    }

    %% Relationships
    users ||--o{ player_library : "owns games"
    games ||--o{ player_library : "owned by users"
    
    games ||--o{ dlc : "has"
    
    users ||--o{ dlc_library : "owns DLC"
    dlc ||--o{ dlc_library : "owned by users"
    games ||--o{ dlc_library : "associated with DLC"

```