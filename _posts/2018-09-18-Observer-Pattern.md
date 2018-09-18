---
layout : post
title : Observer Pattern
comments: true
categories : Java
---

### Observer Pattern

Observer pattern is used when there is one-to-many relationship between objects such as if one object is modified, its depenedent objects are to be notified and updated automatically.

#### Example

For example, if someone like a blog, they can subscribe it.

If there is any updates, he/she can get the updated content after subscribing.
The data before subscribing cannot be received.

If they don't like it, they can unsubscribe it such that they won't receive
any contents from it.

In this case, there is no need for blog(**subject**) to know who(**observer**) interest it and how they handle it. It just notify you because your are a
subscriber.

Some of it’s implementations are;

- java.util.EventListener in Swing
- javax.servlet.http.HttpSessionBindingListener
- javax.servlet.http.HttpSessionAttributeListener

### Class Diagram

![_config.yml]({{ site.baseurl }}/images/observer-pattern.png)

### Code

Following code is from [programcreek](https://www.programcreek.com/2011/01/an-java-example-of-observer-pattern/):

```java
public interface Subject {

	void registerObserver(Observer o);
	void removeObserver(Observer o);
	void notifyAllObservers();

}

public class HeadHunter implements Subject{

		private ArrayList<Observer> userList;
		private ArrayList<String> jobs;

		public HeadHunter(){
			userList = new ArrayList<Observer>();
			jobs = new ArrayList<String>();
		}

		@Override
		public void registerObserver(Observer o) {
			userList.add(o);
		}

		@Override
		public void removeObserver(Observer o) {}

		@Override
		public void notifyAllObservers() {
			for(Observer o: userList){
				o.update(this);
			}
		}

		public void addJob(String job) {
			this.jobs.add(job);
			notifyAllObservers();
		}

		public ArrayList<String> getJobs() {
			return jobs;
		}

		public String toString(){
			return jobs.toString();
		}
}

public interface Observer {

	void update(Subject s);

}

public class JobSeeker implements Observer {

		private String name;

		public JobSeeker(String name){
			this.name = name;
		}

		@Override
		public void update(Subject s) {
			System.out.println(this.name + " got notified!");
			System.out.println(s);
		}
}

public class Test {

	public static void main(String[] args) {

		HeadHunter hh = new HeadHunter();
		hh.registerObserver(new JobSeeker("Mike"));
		hh.registerObserver(new JobSeeker("Chris"));
		hh.registerObserver(new JobSeeker("Jeff"));

		hh.addJob("Google Job");
		hh.addJob("Yahoo Job");
	}

}
```
