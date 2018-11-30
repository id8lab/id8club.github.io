### Project page
<br>
<br>

W have 3 very interesting projects ongoing in the MVP club. They vary in diffuculty and type of technology and skills needed. Students are able to chose what project they want to work on based on there skills and interests.

Please have a look at the projects below and feel free to add content on the project you are working on. These project pages are a work in progress that we all need to contribute to.



{% for item in site.projects_collection %}
  <h2>{{ item.title }}</h2>
  <p>{{ item.description }}</p>
  <p><a href="{{ item.url }}">{{ item.title }}</a></p>
{% endfor %}
