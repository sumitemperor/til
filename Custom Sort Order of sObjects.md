# Custom Sort Order of sObjects
To implement a custom sort order for sObjects in lists, create a wrapper class for the sObject and implement the  Comparable  interface. The wrapper class contains the sObject in question and implements the  compareTo  method, in which you specify the sort logic.

This example shows how to create a wrapper class for Opportunity. The implementation of the  compareTo  method in this class compares two opportunities based on the Amount field—the class member variable contained in this instance, and the opportunity object passed into the method.

        global class OpportunityWrapper implements Comparable {
    
        public Opportunity oppy;
        
        // Constructor
        public OpportunityWrapper(Opportunity op) {
            oppy = op;
        }
        
        // Compare opportunities based on the opportunity amount.
        global Integer compareTo(Object compareTo) {
            // Cast argument to OpportunityWrapper
            OpportunityWrapper compareToOppy = (OpportunityWrapper)compareTo;
            
            // The return value of 0 indicates that both elements are equal.
            Integer returnValue = 0;
            if (oppy.Amount > compareToOppy.oppy.Amount) {
                // Set return value to a positive value.
                returnValue = 1;
            } else if (oppy.Amount < compareToOppy.oppy.Amount) {
                // Set return value to a negative value.
                returnValue = -1;
            }
            
            return returnValue;       
        }
    }
This example provides a test for the OpportunityWrapper class. It sorts a list of OpportunityWrapper objects and verifies that the list elements are sorted by the opportunity amount.

    @isTest 
    private class OpportunityWrapperTest {
        static testmethod void test1() {
            // Add the opportunity wrapper objects to a list.
            OpportunityWrapper[] oppyList = new List<OpportunityWrapper>();
            Date closeDate = Date.today().addDays(10);
            oppyList.add( new OpportunityWrapper(new Opportunity(
                Name='Edge Installation',
                CloseDate=closeDate,
                StageName='Prospecting',
                Amount=50000)));
            oppyList.add( new OpportunityWrapper(new Opportunity(
                Name='United Oil Installations',
                CloseDate=closeDate,
                StageName='Needs Analysis',
                Amount=100000)));
            oppyList.add( new OpportunityWrapper(new Opportunity(
                Name='Grand Hotels SLA',
                CloseDate=closeDate,
                StageName='Prospecting',
                Amount=25000)));
            
            // Sort the wrapper objects using the implementation of the 
            // compareTo method.
            oppyList.sort();
            
            // Verify the sort order
            System.assertEquals('Grand Hotels SLA', oppyList[0].oppy.Name);
            System.assertEquals(25000, oppyList[0].oppy.Amount);
            System.assertEquals('Edge Installation', oppyList[1].oppy.Name);
            System.assertEquals(50000, oppyList[1].oppy.Amount);
            System.assertEquals('United Oil Installations', oppyList[2].oppy.Name);
            System.assertEquals(100000, oppyList[2].oppy.Amount);
            
            // Write the sorted list contents to the debug log.
            System.debug(oppyList);
        }
    }
