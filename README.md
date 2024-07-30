from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///blog.db'
db = SQLAlchemy(app)

class User(db.Model):
id = db.Column(db.Integer, primary_key=True)
username = db.Column(db.String(80), unique=True, nullable=False)

class Post(db.Model):
id = db.Column(db.Integer, primary_key=True)
title = db.Column(db.String(120), nullable=False)
content = db.Column(db.Text, nullable=False)

@app.route('/posts', methods=['POST'])
def create_post():
data = request.get_json()
title = data.get('title')
content = data.get('content')
new_post = Post(title=title, content=content)
db.session.add(new_post)
db.session.commit()
return jsonify({'message': 'Post created successfully'})

@app.route('/posts/<int:post_id>', methods=['GET'])
def get_post(post_id):
post = Post.query.get_or_404(post_id)
return jsonify({'title': post.title, 'content': post.content})

@app.route('/posts/<int:post_id>', methods=['PUT'])
def update_post(post_id):
post = Post.query.get_or_404(post_id)
data = request.get_json()
post.title = data.get('title')
post.content = data.get('content')
db.session.commit()
return jsonify({'message': 'Post updated successfully'})

@app.route('/posts/<int:post_id>', methods=['DELETE'])
def delete_post(post_id):
post = Post.query.get_or_404(post_id)
db.session.delete(post)
db.session.commit()
return jsonify({'message': 'Post deleted successfully'})

if __name__ == '__main__':
db.create_all()
app.run(debug=True)
