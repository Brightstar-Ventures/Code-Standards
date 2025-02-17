# Clean Code Standards

These code standards should be upheld for all projects. Following these guidelines will result in functional, readable, and concise code. Although these standards were originally written for Python, they are applicable to all programming languages.

---

## Table of Contents
- [Respect Synchronousness](#respect-synchronousness)
- [Functions](#functions)
- [Conditionals](#conditionals)
- [Loops](#loops)
- [Typing](#typing)
- [Imports](#imports)

---

## **Respect Synchronousness**

1. Never introduce **blocking** synchronous I/O in an asynchronous execution context.  
   - If a function involves **file operations, database queries, or API calls**, ensure it is properly handled with **async I/O** when running in an asynchronous environment.

---

## **Functions**

1. Functions must do **one thing and one thing only** (aim for 10-20 lines max).  
2. Functions should **not have unintended side effects**.  
3. Functions should have **clear, descriptive names**.  
4. Function names should be **verbs**.  
5. Functions should take **no more than three parameters** unless necessary.  
6. Function body should **only use given parameters** (respect dependency injection and scope).  

    ```python
    # BAD EXAMPLE

    song_names_and_artists: dict[str, str] = {}

    # Violates the parameter limit and modifies an external variable
    def add_song(name: str, artist: str, songs_dict: dict[str, str]) -> None:
        songs_dict[name] = artist
        
    add_song(name="Dogs", artist="Pink Floyd", songs_dict=song_names_and_artists)

    # This function modifies an external variable—bad practice.
    ```

7. Explicitly name function arguments.

    ```python
    def praise_team(member_name: str, praise: str) -> None:
        """Prints praise for a given team member."""
        print(f"{member_name} {praise}!")

    # BAD EXAMPLE
    praise_team("Nathan", "is the best!")

    # GOOD EXAMPLE
    praise_team(member_name="Joel", praise="delivered the feature on time!")

    # Naming arguments improves readability and triggers Python’s built-in validation.
    ```

---

## **Conditionals**

1. **Avoid deeply nested conditionals** (≥3 levels).  
   - Use **early returns or guard clauses** to simplify logic.

2. Keep condition clauses **short and readable** (prefer `symbol == symbol`, avoid inline function calls).

    ```python
    # BAD EXAMPLE
    if member_id == database.get_member_id():
        print(f"Hello {database.get_member_id()}!")

    # GOOD EXAMPLE
    current_member_id: int = database.get_member_id()
    if member_id == current_member_id:
        print(f"Hello {current_member_id}!")

    # Extracting function calls into variables improves readability.
    ```

---

## **Loops**

1. **Avoid nested loops **.  
   - Instead, **refactor logic into helper functions**.

    ```python
    # DO NOT DO THIS
   for country in globe:
   	for city in country:
   		print(f"{city} in {country}")
   
   # DO THIS
   print_all_cities_in_country(country: Country) -> None:
   	for city in country:
   		print(f"{city} in {country}")
   		
   for country in globe:
   	print_all_cities_in_country(country=country)
    ```

   # We do this because instead of having nested loops,
   # we make the code more readable by throwing the "nest"
   # into a clearly typed and strong-contract function.

---

## **Typing**

1. **Use type hints everywhere in function signatures**.

    ```python
    # GOOD EXAMPLE
    def calculate_age(birth_year: int) -> int:
        return 2024 - birth_year
    ```

2. **Use runtime type assertion** (via validation libraries or `assert` statements).

    ```python
    # GOOD EXAMPLE
    def add_numbers(a: int, b: int) -> int:
        assert isinstance(a, int), "a must be an integer"
        assert isinstance(b, int), "b must be an integer"
        return a + b

    # BAD EXAMPLE
    def add_numbers(a: int, b: int) -> int:
        return a + b

    # Without validation, this code runs but misbehaves:
    add_numbers(a="hello", b="world")
    # Returns "helloworld" instead of throwing an error.
    ```

3. **Type hints are encouraged but can be omitted for simple variables**.

    ```python
    # GOOD EXAMPLE
    project_year: int = 2022  # Explicit typing

    # ACCEPTABLE EXCEPTION
    project_year = 2022  # Simple and readable
    ```

---

## **Imports**

1. **Organize imports logically**.

    ```python
    # Built-in
    from typing import Optional

    # External
    from fastapi import FastAPI

    # Internal
    from src.modules.coordination.module import CoordinationModule
    ```

2. **Use explicit imports (`from x import y`)**.

    ```python
    # GOOD EXAMPLE
    from fastapi import FastAPI

    # BAD EXAMPLE
    import fastapi
    ```

---

These guidelines ensure **clean, maintainable, and efficient code**. They apply universally across programming languages and should be strictly followed in all projects.
