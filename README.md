# DataWarehousing
This project based on star schema model which contain the population of fact table from 3 dimentional tables

If some one want to get always updated data from dimensional tables into fact table in such a way that there will be no more duplicate rows in fact table.
Here is the solution by attaching the execution sql task. In this sql we have two choices,
Either include delete statement just before the completion of package.
e.g
delete from FactTable where SnapshotDateKey = convert(int, replace( convert(varchar(10), GETDATE(), 20) ,'-',''))

Or just create a table with datetime column, this table always accept the value from dimentional tables during process of population of fact table and at same time it returns
date values for filter the rows which are not matched with the dimentional table values.

Here is the example statement
e.g 
insert into destinationTable (salary, name, issue_date)
output inserted.issue_date into getSnapshotDate(updated_at)
select salary, name, issue_date from sourceTable 
where issue_date > (select case when exists (select 1 from getSnapshotDate) 
								then (select max(updated_at) from getSnapshotDate) 
								else '2000-01-01' 
					end as updated_at)
          
Just put above statement before the completion of package.
