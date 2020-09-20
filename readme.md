## 使用说明：

页面

```
<div class="form-group">
    <label for="code" class="col-sm-3 control-label">验证码</label>
    <div class="col-sm-5">
        <input type="text" class="form-control" id="code" name="code">
    </div>
    <div class="col-sm-4">
        <img onclick="changeImage(this);" src="/check/code/"  title="点击刷新">
    </div>
</div>
```

视图函数

```
from io import BytesIO

def check_code(request):
    img,code = gen_check_code()
    obj = BytesIO()
    img.save(obj,format='png')
    request.session['check_code'] = code
    return HttpResponse(obj.getvalue())

def login(request):
    if request.method == 'GET':
        return render(request, 'login.html')
    code = request.POST.get('code')
    check_code = request.session.get('check_code')
    if not code:
        return render(request,'login.html',{'error':'请输入验证码'})
    if code.upper() != check_code.upper():
        return render(request,'login.html',{'error':'验证码错误'})
    username = request.POST.get('username')
    password = request.POST.get('Password')
    user_obj = models.UserInfo.objects.filter(username=username, password=password).first()
    if not user_obj:
        return render(request,'login.html',{'err':'用户名密码错误'})
    return redirect(reverse('project_list'))
```

