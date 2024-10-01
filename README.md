# AJAX-edit-update-Laravel




----------------------------------------------------------------------------------------------------
    public function store(Request $request)
    {
        $validatedData = $request->validate([
            'name' => 'required|string|min:3|max:25',
        ]);

        $tag = Tag::create($validatedData);

        return response()->json([
            'status' => true,  //عشان الجافاسكريبت في الفيو يقدر يميز بجملة الشرط الصح من الخطأ
            'message' => 'Tag created successfully!',
            'tag' => $tag  // يمكنك إرجاع البيانات التي تم إنشاؤها إذا كنت تحتاج ذلك
        ]);
    }

----------------------------------------------------------------------------------------------------
 create
@extends('layout.app')

@section('content')
<div class="col-12">
    <center class="mt-4">
        <h1>Add Tag</h1>
    </center>
</div>

<div class="col-8 mx-auto">
    <form id="form-id" action="{{ route('ajaxtag.store') }}" method="POST" class="form border p-3">
        @csrf

        <!-- إضافة عنصر لعرض الرسائل -->
        <div id="show-message" class="alert d-none"></div>

//الكلاس d-none في عنصر الـ <div> مع الـ id="show-message" يستخدم لإخفاء الرسالة بشكل افتراضي عند تحميل الصفحة. 
هذا يساعد على التحكم في عرض الرسالة فقط عندما يكون هناك حاجة إلى إظهارها بعد إجراء معين، مثل تقديم النموذج أو نجاح عملية AJAX.

        <x-flash-message></x-flash-message>
        <x-errors-display></x-errors-display>



        <div class="mb-3">
            <label for="name">Tag Name</label>
            <input type="text" class="form-control" name="name" id="name"
                   value="{{ old('name') }}" required>
        </div>

        <div class="mb-3">
            <button type="submit" class="form-control btn btn-primary">Submit</button>
        </div>
    </form>
</div>

@endsection


@section('script')

<script>
    let formElement = document.getElementById('form-id'); // جميع عناصر النموذج الآن في هذا المتغير
    let messageElement = document.getElementById('show-message');

    formElement.addEventListener('submit', function(e) {
        // يتم استدعاء هذه الدالة عند نقر المستخدم على زر الإرسال
        e.preventDefault();

        let input = document.querySelector('input[name="name"]');	
        let token = document.querySelector('input[name="_token"]');

        // استخدام fetch لإرسال طلب
        fetch(formElement.action, {   // يجلب URL الموجود في سمة "action" للنموذج
            method: "POST",  // يجب أن تكون POST بحروف كبيرة
            headers: {
                'X-CSRF-TOKEN': token.value,
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ name: input.value })  // تحويل البيانات إلى JSON
        })
        .then(function(res) {
            return res.json();  // تحليل الاستجابة كـ JSON
        })
        .then(function(data) {
            messageElement.classList.remove('d-none');  // عرض الرسالة

            if (data['status']) {
                messageElement.classList.remove('alert-danger');
                messageElement.classList.add('alert-success');
                input.value = "";  // إعادة تعيين الحقل إذا كانت الاستجابة ناجحة
            } else {
                messageElement.classList.remove('alert-success');
                messageElement.classList.add('alert-danger');
            }

            messageElement.textContent = data.message;  // عرض الرسالة المستلمة
        })
        .catch(function(error) {
            console.error('Error:', error);  // التعامل مع الأخطاء
        });
    });
</script>


@endsection


OR


<script>
    document.getElementById('form-id').addEventListener('submit', function(e) {
        e.preventDefault();

        let input = document.querySelector('input[name="name"]');
        let token = document.querySelector('input[name="_token"]');
        let messageElement = document.getElementById('show-message');

        fetch(this.action, {
            method: "POST",
            headers: {
                'X-CSRF-TOKEN': token.value,
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ name: input.value })
        })
        .then(res => res.json())
        .then(data => {
            messageElement.classList.remove('d-none', 'alert-success', 'alert-danger');
            messageElement.classList.add(data.status ? 'alert-success' : 'alert-danger');
            messageElement.textContent = data.message;
            if (data.status) input.value = "";
        })
        .catch(error => console.error('Error:', error));
    });
</script>




----------------------------------------------------------------------------------
update




    public function update(Request $request,$id)
    {
        $request->validate([
            'name' => 'required|string|min:3|max:255',
        ]);
        $tag=Tag::findOrFail($id);
        $tag->update($request->all());
        return response()->json(['status' => true,'message' => 'Tag updated successfully!'
    ,            'tag' => $tag  // يمكنك إرجاع البيانات التي تم إنشاؤها إذا كنت تحتاج ذلك

    ]);
    }


-------------------------------------------------
edit



@extends('layout.app')

@section('content')
<div class="col-12">
    <center class="mt-4">
        <h1>Edit Tag</h1>
    </center>
</div>

<div class="col-8 mx-auto">

    <form action="{{ route('ajaxtag.update', $tagId) }}" method="post" class="form" id='form-id'>

                <!-- إضافة عنصر لعرض الرسائل -->
                <div id="show-message" class="alert d-none"></div>

                <x-flash-message></x-flash-message>
                <x-errors-display></x-errors-display>


        @csrf
        @method('PUT')

        <div class="mb-3">
            <label for="name">Tag Name</label>
            <input type="text" class="form-control" name="name" id="name"
                   value="{{ old('name') }}" required>
        </div>

        <div class="mb-3">
            <input type="submit" class="form-control btn btn-primary" value="Save">
        </div>
    </form>
</div>
@endsection


@section('script')

<script>
    let formElement = document.getElementById('form-id'); // جميع عناصر النموذج الآن في هذا المتغير
    let messageElement = document.getElementById('show-message');

    formElement.addEventListener('submit', function(e) {
        // يتم استدعاء هذه الدالة عند نقر المستخدم على زر الإرسال
        e.preventDefault();

        let input = document.querySelector('input[name="name"]');
        let token = document.querySelector('input[name="_token"]');

        // استخدام fetch لإرسال طلب
        fetch(formElement.action, {   // يجلب URL الموجود في سمة "action" للنموذج
            method: "PUT",  // يجب أن تكون POST بحروف كبيرة
            headers: {
                'X-CSRF-TOKEN': token.value,
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ name: input.value })  // تحويل البيانات إلى JSON
        })
        .then(function(res) {
            return res.json();  // تحليل الاستجابة كـ JSON
        })
        .then(function(data) {
            messageElement.classList.remove('d-none');  // عرض الرسالة

            if (data['status']) {
                messageElement.classList.remove('alert-danger');
                messageElement.classList.add('alert-success');
                input.value = "";  // إعادة تعيين الحقل إذا كانت الاستجابة ناجحة
            } else {
                messageElement.classList.remove('alert-success');
                messageElement.classList.add('alert-danger');
            }

            messageElement.textContent = data.message;  // عرض الرسالة المستلمة
        })
        .catch(function(error) {
            console.error('Error:', error);  // التعامل مع الأخطاء
        });
    });
</script>




@endsection



----------------------------------------------------------------------------------
