# Stats By Zach API Documentation

Welcome to the official documentation for the REST API powering [statsbyzach.com](https://statsbyzach.com). This API is built for extracting advanced NHL data in a simpler way than using the publicly available NHL API, as that data needs expensive pre-processing to extract this same info. I made this to abstract that entire process and provide an easy interface to retrieve that data.

All data in the API is sourced from the NHL's API and HTML reports but gets heavily transformed through various of my own scrapers and pipelines and stored in my own database to provide easily accessible data at all levels of the league (game, team, line, pair, player etc). The xG metrics that are present in these endpoints are my own, and if you want more insight on that I will be posting a freshly updated article about that model on my website soon. 

## Base URL  
All endpoints are accessible at:  
**[https://stats-by-zach-api.onrender.com/](https://statsbyzach.com)**

---

## Important Notes

- **Data Updates**:  
  Data is updated daily, with a batch process running around **5:00 AM EST**. Live updates during games are a future enhancement.
  
- **Data Structures**:  
  The data is structured in a way that I designed for my website, so keep that in mind when combing through the data. There are things I could have done better probably, but to me, this is a nice way to easily integrate this data within my site.
  
- **Usage and Requests**:  
  The API infrastructure is funded out of pocket, so please be mindful of your usage. Feel free to explore and use the API, but avoid excessive requests to help keep it accessible for everyone.

- **Future Features**:  
  As new features are added to the site, additional endpoints will be documented here. Be sure to check back regularly for updates.

- **Conventions**:
  Here is a brief index of common conventions you'll see in this API
  
| **Convention** | **Definition**                                                                                       |
|----------------|-----------------------------------------------------------------------------------------------------|
| **`xGF/A`**    | Expected Goals For/Against (a measure of shot quality)                                               |
| **`CF/A`**     | Corsi For/Against (all shot attempts, including blocked shots)                                       |
| **`FF/A`**     | Fenwick For/Against (unblocked shot attempts: shots on goal + missed shots)                          |
| **`SF/A`**     | Shots For/Against (shots on goal)                                                                    |
| **`GF/A`**     | Goals For/Against (actual goals scored)                                                              |
| **`player_id`**| Player ID according to the NHL API                                                                   |
| **`game_id`**  | Game ID according to the NHL API                                                                     |

## Endpoints

### Game Endpoints
All game-specific data can be retrieved using the `/game/{id}` routes.

#### **1. Game Info**
**`GET /game/{id}/info`**  
Provides general information about a specific game. Note - this will also appear as an object on all other /game endpoints as well

- **Path Parameters**:
  - `id` (integer): The unique ID of the game as per the NHL's API.
  
- **Response**:
  ```json
  {
    "game_info": {
      "game_id": 2024020098,
      "date": "2024-11-05",
      "season": 20242025,
      "game_type": 2,
      "home_team": "NYI",
      "away_team": "DET"
    }
  }

#### **2. Game Scoring Plays**  
**`GET /game/{id}/scoring-plays`**  
Provides all scoring plays of a game.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  ```json
  {
    "scoring_plays": [
      {
        "period": 1,
        "period_minutes_elapsed": 3.733,
        "game_minutes_elapsed": 3.733,
        "period_minutes_elapsed_time": "03:44",
        "game_minutes_elapsed_time": "03:44",
        "event_team": "NYR",
        "event_primary_player_name": "CHRIS KREIDER",
        "event_primary_player_id": 8475184,
        "event_secondary_player_name": "MIKA ZIBANEJAD",
        "event_secondary_player_id": 8476459,
        "event_tertiary_player_name": null,
        "event_tertiary_player_id": null,
        "goalie_against_name": "ILYA SOROKIN",
        "goalie_against_id": 8478009,
        "home_score": 0,
        "away_score": 0,
        "strength_rel": "4v5",
        "new_home_score": 1,
        "new_away_score": 0,
        "is_shot_on_empty_net_rel": 0
      }
    ]
  }
  
#### **3. Game Team Stats**  
**`GET /game/{id}/team-stats`**  
Provides team-level statistics for a specific game across different strength situations.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  ```json
  {
    "team_stats": {
      "home_all_sit_xgf": 3.721,
      "home_all_sit_cf": 56,
      "home_all_sit_ff": 45,
      "home_all_sit_sf": 40,
      "home_all_sit_gf": 5,
      "away_all_sit_xgf": 3.872,
      "away_all_sit_cf": 68,
      "away_all_sit_ff": 50,
      "away_all_sit_sf": 37,
      "away_all_sit_gf": 2,
      "home_5v5_xgf": 2.124,
      "home_5v5_cf": 41,
      "home_5v5_ff": 32,
      "home_5v5_sf": 27,
      "home_5v5_gf": 2,
      "away_5v5_xgf": 2.969,
      "away_5v5_cf": 55,
      "away_5v5_ff": 39,
      "away_5v5_sf": 30,
      "away_5v5_gf": 2,
      "home_even_xgf": 2.242,
      "home_even_cf": 45,
      "home_even_ff": 35,
      "home_even_sf": 30,
      "home_even_gf": 2,
      "away_even_xgf": 2.986,
      "away_even_cf": 56,
      "away_even_ff": 40,
      "away_even_sf": 31,
      "away_even_gf": 2
    }
  }
#### **5. Game Shots**
**`GET /game/{id}/shots`**  
Provides detailed shot data for a specific game.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of shot events in the game. Only Fenwick shots included

  ```json
  {
    "shots": [
      {
        "period": 1,
        "event": "shot-on-goal",
        "event_team": "NYI",
        "team_code_against": "NYR",
        "description": "NYI ONGOAL - #8 DOBSON, Snap , Off. Zone, 29 ft.",
        "shot_type": "snap",
        "shot_distance": 29.07,
        "shot_angle": 49.18,
        "xg": 0.061,
        "is_goal": 0,
        "is_home_team": 0,
        "is_shot_on_empty_net_rel": 0,
        "x_coordinate": 70,
        "y_coordinate": -22,
        "x_coordinate_adjusted": 70,
        "y_coordinate_adjusted": -22,
        "home_score": 0,
        "away_score": 0,
        "score_state_rel_cat": "trailing",
        "strength": "5v5",
        "strength_rel": "5v5",
        "strength_cat_rel": "even",
        "situation_code": 1551,
        "home_skaters_on_ice": 5,
        "away_skaters_on_ice": 5,
        "home_goalie_on_ice": 1,
        "away_goalie_on_ice": 1,
        "event_primary_player_name": "NOAH DOBSON",
        "event_primary_player_id": 8480865,
        "event_secondary_player_name": null,
        "event_secondary_player_id": null,
        "event_tertiary_player_name": null,
        "event_tertiary_player_id": null,
        "goalie_against_name": "IGOR SHESTERKIN",
        "goalie_against_id": 8478048
      }
    ]
  }

#### **6. Skater On-Ice Stats**
**`GET /game/{id}/skater-on-ice-stats`**  
Provides detailed on-ice stats for every skater in a specific game, broken down by various strength situations.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of skaters with their on-ice performance metrics across different strength situations.

  ```json
  {
    "skater_on_ice_stats": [
      {
        "player": "KYLE PALMIERI",
        "player_id": 8475151,
        "team": "NYI",
        "all_sit_toi": 17.18,
        "all_sit_cf": 29,
        "all_sit_ca": 13,
        "all_sit_ff": 20,
        "all_sit_fa": 11,
        "all_sit_sf": 14,
        "all_sit_sa": 11,
        "all_sit_xgf": 2.05,
        "all_sit_xga": 1.211,
        "all_sit_gf": 1,
        "all_sit_ga": 2,
        "even_toi": 12.6,
        "even_cf": 20,
        "even_ca": 11,
        "even_ff": 13,
        "even_fa": 9,
        "even_sf": 10,
        "even_sa": 9,
        "even_xgf": 1.45,
        "even_xga": 0.777,
        "even_gf": 1,
        "even_ga": 2,
        "_5v5_toi": 11.92,
        "_5v5_cf": 20,
        "_5v5_ca": 9,
        "_5v5_ff": 13,
        "_5v5_fa": 7,
        "_5v5_sf": 10,
        "_5v5_sa": 7,
        "_5v5_xgf": 1.45,
        "_5v5_xga": 0.686,
        "_5v5_gf": 1,
        "_5v5_ga": 2
      }
    ]
  }
#### **7. Individual Skater Stats**
**`GET /game/{id}/skater-individual-stats`**  
Provides individual performance metrics for each skater in a specific game, broken down by various strength situations.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of skaters with their individual stats across different strength situations.

  ```json
  {
    "skater_stats": [
      {
        "player": "KYLE PALMIERI",
        "player_id": 8475151,
        "team": "NYI",
        "all_sit_ic": "7",
        "all_sit_if": "4",
        "all_sit_is": "4",
        "all_sit_ig": "0",
        "all_sit_ixg": 0.701,
        "all_sit_ia": "1",
        "all_sit_toi": 17.18,
        "even_ic": "6",
        "even_if": "4",
        "even_is": "4",
        "even_ig": "0",
        "even_ixg": 0.701,
        "even_ia": "1",
        "even_toi": 12.6,
        "_5v5_ic": "6",
        "_5v5_if": "4",
        "_5v5_is": "4",
        "_5v5_ig": "0",
        "_5v5_ixg": 0.701,
        "_5v5_ia": "1",
        "_5v5_toi": 11.92,
        "powerplay_ic": "1",
        "powerplay_if": "0",
        "powerplay_is": "0",
        "powerplay_ig": "0",
        "powerplay_ixg": 0,
        "powerplay_ia": "0",
        "powerplay_toi": 4.58,
        "penalty_kill_ic": "0",
        "penalty_kill_if": "0",
        "penalty_kill_is": "0",
        "penalty_kill_ig": "0",
        "penalty_kill_ixg": 0,
        "penalty_kill_ia": "0",
        "penalty_kill_toi": 0
      }
    ]
  }
  
#### **8. Line Stats**
**`GET /game/{id}/line-stats`**  
Provides performance metrics for each forward line in a specific game at 5v5 strength. Only returns lines with a minimum of 3 minutes of 5v5 TOI.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of forward lines with their stats at 5v5 strength.

  ```json
  {
    "line_stats": [
      {
        "team": "NYI",
        "line_id": "8475314-8476419-8477500",
        "line_name": "BO HORVAT - ANDERS LEE - JEAN-GABRIEL PAGEAU",
        "line_name_trimmed": "HORVAT - LEE - PAGEAU",
        "_5v5_cf": "9",
        "_5v5_ca": "9",
        "_5v5_ff": "7",
        "_5v5_fa": "7",
        "_5v5_sf": "5",
        "_5v5_sa": "6",
        "_5v5_gf": "0",
        "_5v5_ga": "0",
        "_5v5_xgf": 0.547,
        "_5v5_xga": 0.214,
        "_5v5_toi": 12.4
      }
    ]
  }
  
#### **9. Pair Stats**
**`GET /game/{id}/pair-stats`**  
Provides performance metrics for each defensive pair in a specific game at 5v5 strength. Only retuens pairs with a minimum of 4 minutes of 5v5 TOI.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of defensive pairs with their stats at 5v5 strength.

  ```json
  {
    "pair_stats": [
      {
        "team": "NYR",
        "pair_id": "8476885-8480817",
        "pair_name": "JACOB TROUBA - K'ANDRE MILLER",
        "pair_name_trimmed": "TROUBA - MILLER",
        "_5v5_cf": "11",
        "_5v5_ca": "21",
        "_5v5_ff": "10",
        "_5v5_fa": "13",
        "_5v5_sf": "8",
        "_5v5_sa": "12",
        "_5v5_gf": "0",
        "_5v5_ga": "1",
        "_5v5_xgf": 0.577,
        "_5v5_xga": 1.395,
        "_5v5_toi": 16.43
      }
    ]
  }

#### **10. Goalie Stats**
**`GET /game/{id}/goalie-stats`**  
Provides performance metrics for goalies in a specific game across all situations.

- **Path Parameters**:
  - `id` (integer): The unique ID of the game, as per the NHL's API.

- **Response**:
  A list of goalie stats for each goalie in the game.

```json
{
  "goalie_stats": [
    {
      "team": "NYR",
      "player_id": 8478048,
      "player": "IGOR SHESTERKIN",
      "all_sit_f_faced": 50,
      "all_sit_s_faced": 37,
      "all_sit_xg_faced": 3.872,
      "all_sit_g_allowed": 2
    }
  ]
}
![image](https://github.com/user-attachments/assets/12a3d9dc-7596-4c00-b472-a887c8d65d96)
