---
layout: post
title: 690. Employee Importance
---
### Question
You are given a data structure of employee information, which includes the
employee's **unique id** , his **importance value** and his **direct**
subordinates' id.

For example, employee 1 is the leader of employee 2, and employee 2 is the
leader of employee 3. They have importance value 15, 10 and 5, respectively.
Then employee 1 has a data structure like [1, 15, [2]], and employee 2 has [2,
10, [3]], and employee 3 has [3, 5, []]. Note that although employee 3 is also
a subordinate of employee 1, the relationship is **not direct**.

Now given the employee information of a company, and an employee id, you need
to return the total importance value of this employee and all his
subordinates.

 **Example 1:**

    
    
     **Input:** [[1, 5, [2, 3]], [2, 3, []], [3, 3, []]], 1
    **Output:** 11
    **Explanation:**
    Employee 1 has importance value 5, and he has two direct subordinates: employee 2 and employee 3. They both have importance value 3. So the total importance value of employee 1 is 5 + 3 + 3 = 11.
    



 **Note:**

  1. One employee has at most one **direct** leader and may have several subordinates.
  2. The maximum number of employees won't exceed 2000.

### Solution 1
    
    
    class Solution {
        public int getImportance(List<Employee> employees, int id) {
            int total = 0;
            Map<Integer, Employee> map = new HashMap<>();
            for (Employee employee : employees) {
                map.put(employee.id, employee);
            }
            Queue<Employee> queue = new LinkedList<>();
            queue.offer(map.get(id));
            while (!queue.isEmpty()) {
                Employee current = queue.poll();
                total += current.importance;
                for (int subordinate : current.subordinates) {
                    queue.offer(map.get(subordinate));
                }
            }
            return total;
        }
    }
    
    
    
    
    class Solution {
        public int getImportance(List<Employee> employees, int id) {
            Map<Integer, Employee> map = new HashMap<>();
            for (Employee employee : employees) {
                map.put(employee.id, employee);
            }
            return getImportanceHelper(map, id);
        }
        
        private int getImportanceHelper(Map<Integer, Employee> map, int rootId) {
            Employee root = map.get(rootId);
            int total = root.importance;
            for (int subordinate : root.subordinates) {
                total += getImportanceHelper(map, subordinate);
            }
            return total;
        }
    }


### Solution 2
The question says `type employees: Employee`, which seems like `employees` is
a node of class `Employee`. However, it's obvious that the class `Employee`
only contain information of 1 employee, and `employees` should contain
information of ALL employees. So I had a hard time figuring out how to extract
every employee's information from `employees` because the question doesn't
really say it. I had to read others' solution to figure out what it actually
means. I don't know whether this is a usual way of describing data type, but I
think this is very confusing. Even `List[Employee]` or `map(Employee)` or
whatever would be a better description.


### Solution 3
Simply convert the employees array/list into a map for easy lookup and then do
a DFS traversal from the "root" employee node.

 _\- Yangshun_

    
    
     class Solution(object):
        def getImportance(self, employees, id):
            """
            :type employees: Employee
            :type id: int
            :rtype: int
            """
            # Time: O(n)
            # Space: O(n)
            emps = {employee.id: employee for employee in employees}
            dfs = lambda id: sum([dfs(sub_id) for sub_id in emps[id].subordinates]) + emps[id].importance
            return dfs(id)         
    

If you wanna make it more readable, it is 5-lines:

    
    
    class Solution(object):
        def getImportance(self, employees, id):
            """
            :type employees: Employee
            :type id: int
            :rtype: int
            """
            # Time: O(n)
            # Space: O(n)
            emps = {employee.id: employee for employee in employees}
            def dfs(id):
                subordinates_importance = sum([dfs(sub_id) for sub_id in emps[id].subordinates])
                return subordinates_importance + emps[id].importance
            return dfs(id)
    



