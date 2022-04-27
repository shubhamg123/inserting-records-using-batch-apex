# inserting-records-using-batch-apex

public class VerifyBatchLimit implements Database.Batchable<sobject>, Database.Stateful {
    // Variable to track processed transactions
    public Integer recordsProcessed = 0;
    
    public Iterable<sObject> start(Database.BatchableContext bc) {
        
        List<account> lstEmployee= new List<account>();
        
        account  empl;
        for(Integer i=0; i<50; i++)
        {
            empl= new account();
            empl.name='Employee Name'+i;
       
            lstEmployee.add(empl);
        }
       
        return lstEmployee;
    }
    
    public void execute(Database.BatchableContext bc, List<account> accunt){
        // process each batch of records
        System.debug(accunt.size());
       
        insert accunt;
    }
    
    public void finish(Database.BatchableContext bc){
       
        AsyncApexJob job = [SELECT Id, Status, NumberOfErrors,
                            JobItemsProcessed,
                            TotalJobItems, CreatedBy.Email
                            FROM AsyncApexJob
                            WHERE Id = :bc.getJobId()];
        
        System.debug(recordsProcessed + ': records processed!');
    }
}
/*
 * global class BatchClassExample implements Database.Batchable<sObject> {
global List<Account> start(Database.BatchableContext BC) {

List<Account> AccList= new List<Account>();
for(Integer i=0;i<500000;i++)
{
account ac= new account();
ac.Name='Batch'+i;
AccList.add(ac);
}
String query = 'SELECT Id, Name FROM Account';
return AccList;
}

global void execute(Database.BatchableContext BC, List<Account> accList) {


try {
if(accList.size()>0)
{
Insert accList;
}

} catch(Exception e) {
System.debug(e);
}

}

global void finish(Database.BatchableContext BC) {
// execute any post-processing operations like sending email
}
}*/
  execute anonymous window
  VerifyBatchLimit myBatchObject = new VerifyBatchLimit();
Id batchId = Database.executeBatch(myBatchObject,10);//
