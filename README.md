### Project
- Restaurant catalog is an application that contains some restaurants with their menus.
- users can login using facebook or google.
- once logged in, user can (add/edit/delete) restaurants and also (add/edit/delete) menu items.
### Database Design
The database consists of three tables (articles, authors and log).
- User Table :
  | Column | Type | Modifiers
  | ------ | ------ | ------ |
  | id | integer | not null |
  | name | string | not null |
  | email | string | not null |
  | picture | string | |
  ```sh
  id is PRIMARY kKEY.
  ```

- Restaurant Table :
  | Column | Type | Modifiers
  | ------ | ------ | ------ |
  | id | integer | not null |
  | name | string | not null |
  | user_id | integer |  |
  ```sh
  id is PRIMARY KEY.
  ```
    ```sh
  user_id is FOREIGN KEY references User Table.
  ```
- MenuItem Table :
  | Column | Type | Modifiers
  | ------ | ------ | ------ |
  | id | integer | not null |
  | name | string | not null |
  | description | string | |
  | price | string | |
  | course | string|  |
  | createdDate | DateTime | not null |
  | restaurant_id | integer |  |
  | user_id | integer|  |
  ```sh
  id is PRIMARY kKEY.
  ```
    ```sh
  user_id is FOREIGN KEY references User Table.
  ```
    ```sh
  restaurant_id is FOREIGN KEY references User Table.
  ```
 ### Code Design
- Project.py contains the routes and endpoint APIs for the project and Login/Logout methods.
- database_setup.py contains the database tables and columns definitions and structure.
- lotsofmenus.py contains static data that will be added to our project.
- templates/allRestaurants.html contains the design to show all restaurants.
- templates/deletemenuitem.html contains the design to delete a menu item.
- templates/deleteRestaurant.html contains the design to delete restaurants.
- templates/editmenuitem.html contains the design to edit menu items.
- templates/editRestaurant.html contains the design to edit restaurants.
- templates/header.html contains the design for the header.
- templates/login.html contains the design for login page.
- templates/main.html contains the design of main page.
- templates/menu.html contains the design for restaurant's menu with operations (add/edit/delete) for logged in users only.
- templates/newmenuitem.html contains the design to add new menu item.
- templates/newRestaurant.html contains the design to add new restaurants.
- templates/publicmenu.html contains the design to show public menus.
- templates/publicrestaurants.html contains the design to show public restaurants.
- templates/restaurants.html contains the design to show all operations (add/edit/delete) restaurants for logged in users only.
### Requirements
- Git Bash (for windows users)
- Terminal (for Mac and Linux users)
- VirtualBox
- Vagrant
### Instructions
- Install Git Bash.
- Install ```Vagrant``` and put it up using ```vagrant up``` command.
### Run the restaurant catalog application
- Open git bash and run the command ``` vagrant ssh ```.
- Go to the project directory.
- Run the command ``` python project.py ``` or ``` python3 project.py ```.
- Go to your browser and type ``` localhost:8000 ```.
### End