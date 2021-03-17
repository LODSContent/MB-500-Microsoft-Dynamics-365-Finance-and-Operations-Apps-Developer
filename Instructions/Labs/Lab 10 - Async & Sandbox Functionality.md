# MB-500: Microsoft Dynamics 365: Finance and Operations Apps Developer

## Lab 10 - Async & Sandbox Functionality

### Lab Overview

-   Dependency: Lab 5 – Code Extension & Development should be completed

-   Convert a regular process to an asynchronous process and execute the same to
    experience the difference

**Estimated time to complete this lab: 30+ minutes**

### Scenario

In Lab 5 – Code Extension & Development, we created a class DDTUpdateTier to
update the Customer Tier. This is a synchronous process and can freeze the
browser in case of a long running activity. To avoid this, we will convert this
process to an asynchronous process by invoking the runAsync() method of formRun.
It will enable us to execute the process in the background without freezing the
browser.

# Exercise: Open Dynamics 365 Finance and Operations apps

## Task 1: Update DDTUpdateTier class

1.  In Visual Studio, in the project DynamicsDevProject, find the class
    DDTUpdateTier

2.  Add this code to the update() method
	1.  Line 1: add int _async=0
	2.  after the commit: Add the if/else
    ```html
      public static int update(int _async=0)
          {
              CustTable           custTable;
              ttsbegin;
              while select forupdate custTable
              {
                  custTable.DDTCustomerTier = DDTTierRange::getTier(CustTable::getTotalMiles(custTable.AccountNum));
                  custTable.update();
              }
              ttscommit;             
              if(_async)
                  info("It was an asynchronous process");
              else
                  info("It was a synchronous process");
             
              return custTable.rowCount();
          }
    ```

3.  Build the project



## Task 2: Form Extension: CustTable

1.  Close the solution and open MyLabAirlines in the Solution Explorer

2.  In the Application Explorer, search for the CustTable form under User
    Interface

3.  Right click CustTable and select **Create Extension**

4.  A new element will be created in the Solution Explorer under the Form
    Extensions folder named CustTable.MyLabAirlines

5.  Open CustTable.MyLabAirlines in designer

6.  Navigate to **Design \| Pattern \> ActionPaneHeader \> aptabGeneral \>
    DDTCustTierButtonGroup**

7.  Add a new button MLACustTierUpdateAsync

    1.  *Text*: Customer Tier Update (Async)

## Task 3: Event Handler for clicked event of the form button

1.  Open MyLabAirlines in Solution Explorer

2.  Right click the project and select **Add \> New Item**

3.  Select **Class under Dynamics 365 Items \> Code**

4.  Create a new class MLACustTableFormEventHandler

5.  Find CustTable.MyLabAirlines in the MyLabAirlines project under Form
    Extensions and open in the designer

6.  Navigate to **Design \| Pattern \> ActionPaneHeader \> aptabGeneral \>
    DDTCustTierButtonGroup \> MLACustTierUpdateAsync \> Events**; right-click
    **onClicked** and Copy event handler method

7.  Paste the method signature within the new class MLACustTableFormEventHandler
    ```html
      [FormControlEventHandler(formControlStr(CustTable, MLACustTierUpdateAsync), FormControlEventType::Clicked)]
      public static void MLACustTierUpdateAsync_OnClicked(FormControl sender, FormControlEventArgs e)
      {
      } 
    ```
 
8.  Add the following two lines within those brackets to execute the
    asynchronous code
    ```html
      FormRun formRun = sender.formRun() as FormRun;
              formRun.runAsync(classNum(DDTUpdateTier),"update",[1], System.Threading.CancellationToken::None);
    ```



## Check Output

**Build Solution**. Once you click the newly created button **Customer Tier
Update (Async)** in the Customer form, there won’t be any visible change. In few
minutes, you should be able to see a message “It was an asynchronous process” in
the **Action Center**. It means the process is already executed. You can check
the Customer Tier in the Customer form to verify the correct execution of the
process.
