# car-dealer-ship
### Overview of project
I made a car dealer ship project for a car sales man. This project will include a dealer ship and vehicles. The project will allow the user to add, remove, and search for cars in the inventory. 

UML for project:
![umlForProject](Images/diagramSS.png)

## Phase 1

I started by working on the Dealership class and my vehicle class. In my Dealer ship class i added get all vehicles functionaly, add or remove vehicles from the inventory. I added test cases for get all vehicles and add a vehicle.

## Phase 2

I added the functionalty that will allow the program to read all the data in the csv file when the application runs. I also made the application save the data that was added, or removed to the from memorey to the csv file when the application gets closed.

## Phase 3

I added the application user interface to allow the user to interacte with the application. I made the menu with all the list options for the user. 

## Phase 4

I added functionalty to the user interface dispatching commands to the application's backend allowing the user to interact with the data in the csv file. 

## List menu

1) List All vehicles

When the user enters "1" it will dispatch that command to the backend and load all the vehicles from the dealer ship class to the userInterface class, from the user interface class the vehicles get displayed on the screen.

2) Add a vehicle

When the user enters "2". The user will be prompted for the vehicle information. Once the user provides the nessary information about the vehicle it will be added to the dealer ships inventory. 

3) Remove a vehicle

When the user enters "3". The user will be prompted for the vin number for the vehicle they want to remove. Then it will search for the vehicle with the same exact vin number and remove it from the inventory.

4) Find vehicles within a price range

 Once the user enters "4". The user will be prompted for the minimum and maximum price range. Once the user provides that information. The program will search throught the dealer ship inventory and check if the vehicle is within that price range. Then it will get displayed on to the screen.

5) Find vehicles by make / model

When the user enters "5". The user will be prompted for the make and model of the vehicle. After the user provides the make and model. The program will search for that vehicle with the make and model name. Then it will display all the vehicles that match that name on to the screen.

6) Find vehicles by year range

When the user enters "6". The application will prompt the user for start year and end year. Once the user provides that information it will search in the data base and check if the inventory has a car equal to or between that year range. Then it will be displayed to the screen. 

7) Find vehicles by color

When the user enters "7". The application will prompt the for vehicle color. Then it will search in the car dealership inventory for the vehicle with color provided. It will then display it on to the screen.

8) Find vehicles by mileage range

Once the user enters "8". The application will prompt the user for starting mileage and ending mileage range. Then the program will dispatch to the backend and search for vehicles with in that mile range. 

9) Find vehicles by type (Car, truck, SUV, van)

When the user enters "9". The application will prompt the user for vehicle type. Then it will search in dealership inventory and check if there is a vehicle with same vehicle type provided. Once it finds it will be displayed on to the screen. 

0) Save and quit

Last but not least, once the user enters "0" the application will save all the data the was removed or added to the csv file and then close the application. 

Code used to fetch and save date:
```java
public class FileManager {

    public static DealerShip getDealership()
    {
        DealerShip dealerShip = null;

        //reading file
        File inventoryFile = new File("files//inventory.csv");
        try(
                FileReader filereader = new FileReader(inventoryFile);
                Scanner reader = new Scanner(filereader)
        )
        {
            // getting dealership from first line of the file
            String firstLine = reader.nextLine();
            String[] dealerShipColumns = firstLine.split("[|]");

            String dealerShipName = dealerShipColumns[0];
            String dealerShipAddress = dealerShipColumns[1];
            String dealerShipPhone = dealerShipColumns[2];

            dealerShip = new DealerShip(dealerShipName, dealerShipAddress, dealerShipPhone);

            while(reader.hasNextLine())
            {
                // reading the rest of the lines
                String line = reader.nextLine();
                String[] vehicleColumns = line.split("[|]");

                int vin = Integer.parseInt(vehicleColumns[0].strip());
                int year = Integer.parseInt(vehicleColumns[1].strip());
                String make = vehicleColumns[2].strip();
                String model = vehicleColumns[3].strip();
                String vehicleType = vehicleColumns[4].strip();
                String color = vehicleColumns[5].strip();
                int odometer = Integer.parseInt(vehicleColumns[6].strip());
                double price = Double.parseDouble(vehicleColumns[7].strip());

                // creating a vehicle object with the data from the csv file
                Vehicle vehicle = new Vehicle(vin, year, make, model, vehicleType, color, odometer, price);

                // adding vehicle to inventory
                dealerShip.addVehicle(vehicle);
            }
        }
        catch (IOException e)
        {
            System.out.println("File does not exist");
        }
        catch (Exception e)
        {
            System.out.println("Something went wrong");
        }
        return dealerShip;
    }

    public static void saveDealerShip(DealerShip dealerShip)
    {

        File inventoryFile = new File("files/inventory.csv");

        // saving data to the csv file when the user leaves
        try(
                FileWriter fileWriter = new FileWriter(inventoryFile);
                PrintWriter writer = new PrintWriter(fileWriter)
                )
        {
            // getting the dealership name and writing it to the file
            String dealerShipName = dealerShip.getName();
            String dealerShipAddress = dealerShip.getAddress();
            String dealerShipPhone = dealerShip.getPhone();

            writer.printf(" %s | %s | %s \n", dealerShipName, dealerShipAddress, dealerShipPhone);

            ArrayList<Vehicle> inventory = dealerShip.getAllVehicles();

            // getting the vehicles in the inventory and writing it to the file
            for(Vehicle vehicle : inventory)
            {
                writer.printf(" %d | %d | %s | %s | %s | %s | %d | %.2f \n", vehicle.getVin(), vehicle.getYear(), vehicle.getMake(), vehicle.getModel(), vehicle.getVehicleType(), vehicle.getColor(), vehicle.getOdometer(), vehicle.getPrice());
            }

        }
        catch (IOException e)
        {
            System.out.println("File does not exist");
        }
        catch (Exception e)
        {
            System.out.println("Something went wrong");
        }
    }
}
```

Sample code for filtering the data with price range:

```java
public void findVehicleWithPriceRange(DealerShip dealerShip)
    {
        try
        {
            // getting min and max price
            System.out.println();
            System.out.print("Enter minimum price: ");
            double minPrice = userInput.nextDouble();
            userInput.nextLine();

            System.out.print("Enter maximum price: ");
            double maxPrice = userInput.nextDouble();
            userInput.nextLine();

            // searching within price range

            ArrayList<Vehicle> priceRangeVehicles = (ArrayList<Vehicle>) dealerShip.getAllVehicles().stream()
                    .filter(vehicle -> vehicle.getPrice() >= minPrice)
                    .filter(vehicle -> vehicle.getPrice() <= maxPrice)
                    .collect(Collectors.toList());

            System.out.println();
            System.out.println("-----------------------------------------Vehicles by price range------------------------------------");
            System.out.printf(" %-10s | %-10s | %-10s | %-10s | %-10s | %-10s | %-10s | %-10s \n", vinTitle, yearTitle, makeTitle, modelTitle, typeTitle, colorTitle, odometerTitle, priceTitle);
            System.out.println("-".repeat(100));
            for(Vehicle vehicle : priceRangeVehicles)
            {
                System.out.printf(" %-10d | %-10d | %-10s | %-10s | %-10s | %-10s | %-10d | %.2f \n", vehicle.getVin(), vehicle.getYear(), vehicle.getMake(), vehicle.getModel(), vehicle.getVehicleType(), vehicle.getColor(), vehicle.getOdometer(), vehicle.getPrice());
                System.out.println("-".repeat(100));
            }
            if(priceRangeVehicles.isEmpty())
            {
                System.out.println("No vehicles found");
            }

        }
        catch (NumberFormatException e)
        {
            System.out.println();
            System.out.println("Please enter your input in numbers");
        }
        catch (Exception e)
        {
            System.out.println();
            System.out.println("Something went wrong, try again");
        }
    }
```

## Test Cases

I added test cases for the dealerShip to check if all the search operations are working correctly.

I think this the unit test are pretty interesting because if you added code to your program and something fails you get to look at the error because of the unit tests you had.
Sample test: 

```java
    @Test
    public void getVehiclesFindVehiclesByYearRange_shouldReturn_allVehiclesWithInTheYearRange()
    {
        //arrange
        DealerShip dealerShip = FileManager.getDealership();
        int startingYear = 2015;
        int endingYear = 2020;

        //act
        ArrayList<Vehicle> vehiclesByMakeAndModel = dealerShip.getVehiclesByYearRange(startingYear, endingYear);

        //assert
        for(Vehicle vehicle : vehiclesByMakeAndModel)
        {
            assertTrue(vehicle.getYear() >= startingYear && vehicle.getYear() <= endingYear);
        }
    }
```

Example of how to use the application:

![listAllVehicles](Images/listAllVehiclesSS.png)


To use this application you will run it in the terminal and choose any of the list option depending on what you want to do.
