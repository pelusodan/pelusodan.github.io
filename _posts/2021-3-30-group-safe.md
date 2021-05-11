---
layout: post
title: "GroupSafe"
author: "Dan Peluso"
categories: programming
tags: [flask, python, html, css, sql, uml]
image: group_safe/cover.png
---
## Overview

GroupSafe is a web application built using Flask designed to track the testing status of members of a group. While the pandemic is the primary motivation for the project, it was designed to be adaptable to any illness or tracking system. I worked alongside my 5 other group members in Spring 2021 to follow the full software development lifecycle for Software Engineering (EECE4520).

## Features

#### User Bio
![](\assets\img\group_safe\bio.png)

#### Create Group
![](\assets\img\group_safe\create_group.png)

#### View Group
![](\assets\img\group_safe\view_group.png)

#### Welcome Page
![](\assets\img\group_safe\welcome.png)

## Code

This project uses SQLAlchemy to connect Python Flask routes to endpoints that perform our CRUD operations. The front end uses the Jinja2 framework to add Python to our HTML frameworks. This was particularly helpful for using Flask form libraries to build parts of the site conditionally.

#### Routing Example

```python
# Endpoint for creating a group
@app.route("/create_group", methods=['GET', 'POST'])
@login_required
def create_group():
    form = CreateGroupForm()
    if form.validate_on_submit():
        if Group.query.filter_by(group_name=form.group_name.data).first()
         is None:
            group = Group(
                group_name=form.group_name.data,
                policy=form.policy.data,
                group_bio=form.group_bio.data)
            # need to add to database to get id
            db.session.add(group)
            db.session.commit()
            updatedGroup = get_group_from_name(group.group_name)
            user_group = UserGroup(
                user_id=current_user.id,
                group_id=updatedGroup.id,
                is_admin=True,
                status_enum=StatusEnum.Untested
            )
            db.session.add(user_group)
            db.session.commit()
            flash('Group: ' + form.group_name.data + ' added')
            return redirect(url_for('home'))
        else:
            flash('Group: ' + form.group_name.data + ' already exists!', category="error")

    return render_template('create_group.html', form=form)

```

#### Flask Form Example
```Python
class CreateGroupForm(FlaskForm):
    group_name = StringField('Group Name',
      validators=[DataRequired(), Length(max=30)])
    policy = StringField('Policies (comma separated)',
      validators=[DataRequired(), Length(min=1)])
    group_bio = StringField('Group Bio',
      validators=[DataRequired()])
    submit = SubmitField('Create')

```

#### Database Model Example
```Python
class Group(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    group_name = db.Column(db.String(20), unique=True, nullable=False)
    policy = db.Column(db.String(256), nullable=False)
    group_bio = db.Column(db.String(256), nullable=False)
    users = relationship("UserGroup", cascade="all,delete",
     back_populates="group")

    def __repr__(self):
        return f"Group('{self.id}', '{self.group_name}', '{self.policy}',
         '{self.group_bio}')"

```

### Skills
- Flask
- Python
- MySQL
- Jinja2
- SQLAlchemy
- Canvas Systems Modeler
- UML

### Source Code

Check out my project on [Github!](https://github.com/pelusodan/GroupSafe)
