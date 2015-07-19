---
layout: post
title: Serialize JSON Data for Bootstrap-treeview
modified:
categories: it
excerpt:
tags: []
image:
  feature:
date: 2015-07-19T11:16:52-10:00
---


### Purpose
To dynamically populate the treeview data with a LINQ-to-SQL datacontext in code behind.The code snippet is called by the front-end javascript to serialize two lists from the database query and merged to create the correct JSON format.

#### Namespaces
{% highlight csharp %}
using System.Runtime.Serialization.Json;
using System.IO;
using System.Text;
using Newtonsoft.Json;
{% endhighlight %}

#### Object Class Instances
{% highlight csharp %}
public class ParentWithNoChild
{
    public string text { get; set; }
}

public class ParentWithChildren
{
    public string text { get; set; }
    public Child[] nodes { get; set; }
}

public class Child
{
    public string text { get; set; }
}
{% endhighlight %}

#### Code behind method
{% highlight csharp %}

public string GetJsonData()
{
    string serializeJsonData = string.Empty;  
    RAP_IRNSIdentityDataContext db = new RAP_IRNSIdentityDataContext();
    List<RapAgency> agencies = (from a in db.RapAgencies
                                select a).ToList();

    int totalAgencies = agencies.Count();

    for (int i = 0; i < totalAgencies; i++)
    {

        List<RapSubAgency> subagencies = (from sa in db.RapSubAgencies
                                            where sa.RapAgencyId == agencies[i].Id
                                            select sa).ToList();

        int totalSubAgencies = subagencies.Count();
        if (totalSubAgencies == 0)
        {
            ParentWithNoChild single = new ParentWithNoChild();
            single.text = agencies[i].Agency;
            serializeJsonData = serializeJsonData.MergeJsonString(JsonConvert.SerializeObject(single));
            //Response.Write("<br/></br> serializeJsonData: " + serializeJsonData);
        }
        else if (totalSubAgencies >= 1)
        {
            ParentWithChildren parent = new ParentWithChildren();
            Child[] children = new Child[totalSubAgencies];

            parent.text = agencies[i].Agency;
            for (int j = 0; j < totalSubAgencies; j++)
            {
                //Response.Write("<br/></br> SubAgencies: " + subagencies[j].AgencyDept);
                if (!String.IsNullOrEmpty(subagencies[j].AgencyDept))
                {
                    Child child = new Child { text = subagencies[j].AgencyDept };
                    children[j] = child;
                }
            }
            parent.nodes = children;
            serializeJsonData = serializeJsonData.MergeJsonString(JsonConvert.SerializeObject(parent));
            //Response.Write("<br/></br> serializeJsonData: " + serializeJsonData);
        }
    }
    return serializeJsonData.GetTreeViewJsonFormat();
}
{% endhighlight %}

#### Javascritp function call.
The Javascript function call which parses the serialized json string back to JSON format.
{% highlight javascript %}
var $tree = $('#treeview12').treeview({
                data: jQuery.parseJSON('<%=GetJsonData() %>')
            });
{% endhighlight %}