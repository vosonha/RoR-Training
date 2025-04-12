# NUS Technology

# Rails Performance - Problems and Solutions

### Table of Contents

- [1. Tips & Tricks](#1)
- [2. Advanced techniques](#2)
- [3. Tools](#3)

### <span id='1'></span>1. Tips & Tricks

#### Prevent heavy CSS + JS

```html
<head>
  <script src=“/assets/application.js”></script>
</head>
<body>
  Content
</body>
```

- Load JS before closing **&lt;/body&gt;**
- Load javascript asynchronously with async attribute

  ```html
  <!-- HTML 5 only -->
  <script src="“”" async="“true”"></script>
  ```

- Load javascript asynchronously with [headjs](http://headjs.com/)

###### Note: Attention: inline JS?

#### N+1 query

```
class User < ActiveRecord::Base
  has_many :comments!
end

class Comments < ActiveRecord::Base
  belongs_to :user
end

#Bad
@comments = Comment.all
@comments.each do |comment|
  comment.user.name!
end

#Good
@comments = Comment.includes(:user).all
@comments.each do |comment|
  p comment.user.name!
end
```

#### Static assets

- Different hosting server for static assets (allow parallel downloads)

```
config.action_controller.asset_host = "example.com"
```

- CDN (AWS CloudFront, RackSpace CloudFiles)
  ![CDN.jpg](CDN.jpg)

#### DB Indexes

- Appropriate indexes give 100x performance gain on **large tables!**
- Don’t add too many, increase DB size and affect performance when updating/inserting!
- Candidates: foreign key, attribute in WHERE condition

#### Background worker

- Delayed_job, Sidekiq, Resque

```
"What if one Sidekiq process could do the work of 20 Resque or DelayedJob processes?"!
```

- Always put time-consuming tasks in background. Ex: sending email, crawling data

#### Faster JSON renderer

- Using **"oj"** gem

```
person = Person.first!
Oj.dump(person, mode: :compat)
```

[http://www.ohler.com/dev/oj_misc/performance_compat.html](http://www.ohler.com/dev/oj_misc/performance_compat.html)

#### Group SQL transactions

```
#Bad
my_collection.each do |name|
  Person.create({:name => name})
end

#Good
Person.transaction do
  my_collection.each do |name|
    Person.create({:name => name})
  end
end

```

#### Caching

- Use faster cache store: _Redis_ or _Memcached_

- Fragment caching

```html
<% cache(key, :expires_in => 1.hour) do %>!
<!-- content -->! <% end %>
```

### <span id='2'></span>2. Advanced Techniques

#### JS optimization

- Render block of HTML instead of insert individual elements

```
#Bad
for (var i=0; i<1000; i++){
  $(“#test”).append(“<span>” + i + “</span>”);
}

#Good
html = “”;
for (var i=0; i<1000; i++){
  html += “<span>” + i + “</span>”;
}
$(“#test”).append(html);
```

- Remove ghost views (**Backbone**)

```
# Bad
# What wrong? Events on elements and sub elements are bound twice
var view = new MyView();
//after doing something crazy!
var view = new MyView();

#Good
#To do: call view.remove() before initializing
var view = new MyView();
//after doing something crazy
view.remove();
var view = new MyView();

```

### <span id='3'></span>3. Tools

- [Google PageSpeed](https://developers.google.com/speed/pagespeed/)
- [Yahoo YSlow](https://developer.yahoo.com/yslow/)
- [NewRelic](http://newrelic.com/)

<table width="100%" cellpadding="4" cellspacing="0"><colgroup><col width="256*"></colgroup></table>
<table width="100%" cellpadding="4" cellspacing="0" style="display: table">
  <colgroup><col width="256*"></colgroup>
  <tbody width="100%">
    <tr width="100%">
      <td width="100%" valign="top" style="border: none; padding: 0in">
        <font color="#000000">
          <font face="Arial, sans-serif">
            <font size="2" style="font-size: 9pt">
              Copyright © 2015, NUS Technology Co., Ltd. All Rights Reserved.
            </font>
          </font>
        </font>
      </td>
    </tr>
  </tbody>
</table>
