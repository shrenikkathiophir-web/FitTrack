# FitTrack - Fitness & Nutrition Tracker (Java Swing)

A desktop application with a dark "gym dashboard" look (charcoal background, neon green accent).
Track workouts, meals, water intake and body metrics. All data is saved automatically.

Uses **only the standard Java library** - no downloads, no external JARs.

## How to Run
Requires JDK 8 or higher (tested on JDK 21).

```
javac *.java
java Main
```
Or double-click `run.bat` (Windows) / run `./run.sh` (Mac/Linux).

Tip: click **Load Demo Data** in the sidebar to fill the app with a week of sample data.

## Screens
| Screen | What it does |
|---|---|
| Dashboard | Today's calories burned/eaten, workout streak, BMI, two 7-day bar charts, recent workouts |
| Workouts | Log a workout (calories are calculated automatically), view history, delete entries |
| Nutrition | Log meals, calorie ring, protein/carbs/fat bars, water tracker (+250 / +500 ml) |
| Profile | Edit age, weight, height, goal; BMI, BMR, daily calorie and macro targets update instantly |

## The Maths
- **BMI** = weight (kg) / height (m)^2   -> Underweight / Normal / Overweight / Obese
- **BMR** (Mifflin-St Jeor) = 10 x weight + 6.25 x height - 5 x age + 5 (male) or - 161 (female)
- **Daily calorie goal** = BMR x 1.4 (light activity) + goal adjustment (-500 lose, 0 maintain, +300 gain)
- **Calories burned** = MET x weight (kg) x hours  (each workout type has its own MET value)
- **Macro goals**: protein 1.6 g/kg, fat 25% of calories, carbs = the rest

## Project Structure (25 Java files)
| File | Role |
|---|---|
| `Main.java` | Entry point: loads data, opens the window |
| `AppPanel.java` | Sidebar + screen switching (CardLayout) |
| `DashboardPanel`, `WorkoutPanel`, `NutritionPanel`, `ProfilePanel` | The four screens |
| `ChartPanel`, `RingPanel`, `MacroBar`, `RoundedPanel` | Custom-drawn components (Java 2D graphics) |
| `DataStore.java` | All data + calculations + file saving/loading |
| `Entry.java` (abstract) -> `Workout.java`, `Meal.java` | Logged items |
| `Profile.java` | User body data, implements `HealthMetrics` |
| `HealthMetrics`, `Refreshable` | Interfaces |
| `WorkoutType`, `MealType`, `Goal`, `Gender` | Enums |
| `Validator.java`, `InvalidInputException.java` | Input checking + custom exception |
| `Theme.java`, `UI.java` | Colours, fonts and styled-component helpers |

Data is stored in `fittrack_data.txt` (created next to the program on first save).

## Java Concepts Demonstrated
| Concept | Where |
|---|---|
| Classes & objects | Every class |
| Encapsulation | Private fields + getters in `Profile`, `Workout`, `Meal`, `Entry` |
| Inheritance | `Workout` and `Meal` extend `Entry`; screens extend `JPanel` |
| Abstraction | Abstract class `Entry`; interfaces `HealthMetrics`, `Refreshable` |
| Polymorphism | `getCalories()`, `getCategory()`, `toFileString()` overridden per subclass; `paintComponent()` overrides |
| Enums with fields & methods | `WorkoutType` (MET value + calorie formula), `Goal` |
| Generics | `JComboBox<WorkoutType>`, `<T extends JPanel & Refreshable>` in `AppPanel` |
| Collections | `ArrayList`, `HashMap`, `LinkedHashMap`, `Comparator` sorting |
| Exception handling | Custom `InvalidInputException`, try/catch, try-with-resources |
| File I/O | `BufferedReader` / `BufferedWriter` in `DataStore` |
| GUI (Swing/AWT) | Layout managers (Border, Grid, Flow, Box, Card), event listeners (lambdas) |
| Java 2D graphics | Bar chart, donut ring and progress bars drawn from scratch |
| `java.time` API | `LocalDate`, `DayOfWeek` for dates, streaks and weekly charts |
| Static utility classes | `Validator`, `UI`, `Theme` |
| Separation of concerns | Data/logic (`DataStore`, models) separate from screens |

## Possible Viva Questions
- **Why is `Entry` abstract?** A plain Entry makes no sense; it holds shared data (date, name) and forces each child to define its calories and file format.
- **Interface vs abstract class?** `Entry` shares state and code; `Refreshable` / `HealthMetrics` only define behaviour a class promises to provide.
- **Why enums?** They restrict values to a fixed set and can carry data (MET value, calorie adjustment).
- **How is data saved?** `DataStore.save()` writes one line per record (`WORKOUT|date|...`); `load()` reads and rebuilds objects. try-with-resources closes the files.
- **How do the charts work without a library?** They extend `JPanel` and override `paintComponent()`, drawing rectangles and arcs with `Graphics2D`.
- **What does the lambda `e -> addWorkout()` do?** It is an `ActionListener` written in short form; it runs when the button is clicked.
- **Why validate input in a separate class?** One place for all rules, and every screen reuses it.
- **Future improvements:** SQLite + JDBC instead of a text file, JFreeChart for charts, user login, weight-progress chart.
