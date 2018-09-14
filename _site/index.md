## Ruby on Rails

### Recipes

<ul>
  {% for ruby_on_rails in site.ruby_on_rails %}
    {% if ruby_on_rails.section == 'Recipes' %}
      <li><a href="{{ ruby_on_rails.url}}">{{ ruby_on_rails.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

### Testing

<ul>
  {% for ruby_on_rails in site.ruby_on_rails %}
    {% if ruby_on_rails.section == 'Testing' %}
      <li><a href="{{ ruby_on_rails.url}}">{{ ruby_on_rails.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>