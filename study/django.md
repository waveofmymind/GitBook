# 🐍 Django

## MTV 패턴

{% hint style="info" %}
Model-Templete-View
{% endhint %}

쟝고에서 코드를 분리하는 기본적인 단위로 사용되며, 하나씩 알아보자.

### Model

장고와 DB를 연결해주는 역할을 한다.

데이터의 형태를 띄며, 일반적으로 DB 테이블과 매핑되게 사용한다.

* 파이썬의 class를 사용하며, 모든 Model 클래스는 django.db.models.Model 클래스를 상속 받는다.
* 각각의 모델 속성은 DB의 필드를 나타낸다.

파일 명은 기본적으로 models.py를 사용한다.

```python
class DjangoModel(models.Model):
    name = models.CharField("이름")
```

### Template

사용자에게 제공될 결과물의 형태를 나타낸다.

html을 보통 많이 사용하며, templates 폴더에서 탐색해서 응답으로 내려준다.

### View

사용자의 요청을 받아 도메인 로직을 수행한다. 파이썬의 함수를 사용한다.

view.py를 주로 사용한다.

```python
def django_view(request):
    return HttpResponse("Django View")
```
