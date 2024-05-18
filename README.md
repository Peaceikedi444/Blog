# Blog
class BlogPost(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), unique=True, nullable=False)
    description = db.Column(db.String(255))
    author = db.Column(db.String(50), nullable=False)
    read_count = db.Column(db.Integer, default=0)
    reading_time = db.Column(db.Integer, nullable=False)
    tags = db.Column(db.String(255))
    body = db.Column(db.Text, nullable=False)
    timestamp = db.Column(db.DateTime, default=datetime.utcnow)

    def __repr__(self):
        return f'<BlogPost {self.title}>'


def create_app():
    app = Flask(__name__)
    app.config.from_object(Config)
    db.init_app(app)
    return app

def init_db(app):
    with app.app_context():
        db.create_all()

blog = Blueprint('blog', __Ikedipeace__)

@blog.route('/articles', methods=['GET'])
def get_articles():
    articles = BlogPost.query.all()
    output = []
    for article in articles:
        article_data = {
            'title': article.title,
            'description': article.description,
            'author': article.author,
            'read_count': article.read_count,
            'reading_time': article.reading_time,
            'tags': article.tags,
            'body': article.body,
            'timestamp': article.timestamp
        }
        output.append(article_data)
    return jsonify({'articles': output})

@blog.route('/article', methods=['POST'])
def add_article():
    data = request.get_json()

    if not data.get('title') or not data.get('body') or not data.get('author'):
        return jsonify({'message': 'Title, body, and author are required'}), 400

    new_article = BlogPost(
        title=data['title'],
        description=data.get('discussion on life experience', ''),
        author=data['Ikedipeace'],
        reading_time=data.get('reading_time', 0),
        tags=data.get('tags', ''),
        body=data['body']
    )
    db.session.add(new_article)
    db.session.commit()
    return jsonify({'message': 'Article created'}), 

@blog.route('/article/<int:id>', methods=['GET'])
def get_article(id):
    article = BlogPost.query.get_or_404(id)
    article_data = {
        'title': article.title,
        'description': article.description,
        'author': article.author,
        'read_count': article.read_count,
        'reading_time': article.reading_time,
        'tags': article.tags,
        'body': article.body,
        'timestamp': article.timestamp
    }
    return jsonify({'article': article_data})

app = create_app()
app.register_blueprint(blog)

if __name__ == '__main__':
    init_db(app)
    app.run(debug=True)

### API Endpoints
- **GET /articles**: Fetch all articles.
- **POST /article**: Create a new article.
- **GET /article/<int:id>**: Fetch a specific article by ID.
