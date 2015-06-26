Bài viết này liệt kê những cách viết code theo phong cách Ruby, ngắn gọn và dễ nhìn hơn. 

# Đặt if ở phía sau để rút gọn

```ruby
if user.active?
  send_mail_to(user)
end
```

```ruby
send_mail_to(user) if user.active?
```

# Dùng unless thay cho if + not

```ruby
user.destroy if !user.active?
```

```ruby
user.destroy unless user.active?
```

Tuy nhiên, nếu điều kiện phía sau unless phức tạp, có `and`, `or`, thì nên dùng `if` cho dễ hiểu. 

```ruby
user.destroy unless (user.active? || user.admin?) && !user.spam?
```

# Sử dụng cách viết điều kiện thu gọn

```ruby
if user.admin?
  "I appreciate for that."
else
  "Thanks."
end
```

```ruby
user.admin? ? "I appreciate for that." : "Thanks"
```

Tuy nhiên, chỉ nên dùng cho những đoạn code có điều kiện đơn giản, không nên dùng với những đoạn code lồng nhau. 

```ruby
# khó đọc
user.admin? ? user.active? ? "I appreciate for that." : "Are you OK?" : "Thanks."
```

# Dùng if đồng thời với phép gán

```ruby
user = find_user
if user
  send_mail_to(user)
end
```

```ruby
if user = find_user
  send_mail_to(user)
end
```

Tuy nhiên, cách viết này có thể gây hiểu nhầm cho người đọc: "lỗi type gõ thiếu dấu `=`, đáng ra phải là `==` hoặc `!=` chứ". Vì thế có người thích mà cũng có người không thích cách viết này. 

# Xác nhận điều kiện từ các class con

```ruby
if parent.children
  if parent.children.singleton?
    singleton = parent.children.first
    send_mail_to(singleton)
  end
end
```

Viết gọn lại 
```ruby
if parent.children && parent.children.singleton?
  singleton = parent.children.first
  send_mail_to(singleton)
end
```


# Không dùng return ở cuối method

Các ngôn ngữ khác phải cần, nhưng với ruby, cách viết không có return có vẻ được yêu thích hơn. 

```ruby
def build_message(user)
  message = 'hello'
  message += '!!' if user.admin?
  return message
end
```

```ruby
def build_message(user)
  message = 'hello'
  message += '!!' if user.admin?
  message
end
```

# Sử dụng Object#tap

```ruby
def build_user
  user = User.new
  user.email = "hoge@hoge.com"
  user.name = "Taro Yamada"
  user
end
```

```ruby
def build_user
  User.new.tap do |user|
    user.email = "hoge@hoge.com"
    user.name = "Taro Yamada"
  end
end
```


# Khi ghép các chuỗi kí tự, không dùng "+” mà dùng "#{ }"

```ruby
"Hello, " + user.name + "!"
```

```ruby
"Hello, #{user.name}!"
```

## freeze các hằng số

`freeze` là cách khai báo một hằng số. Dùng cách này để tránh trường hợp hằng số bị thay đổi trong quá trình làm việc. 

## Chuỗi kí tự

```ruby
CONTACT_PHONE_NUMBER = "03-1234-5678"
CONTACT_PHONE_NUMBER << "@#$%^"
puts CONTACT_PHONE_NUMBER # => 03-1234-5678@#$%^
```

```ruby
CONTACT_PHONE_NUMBER = "03-1234-5678".freeze
CONTACT_PHONE_NUMBER << "@#$%^" # => RuntimeError: can't modify frozen String
```

## array

```ruby
ADMIN_NAMES = ["Tom", "Alice"]
ADMIN_NAMES << "Taro"
puts ADMIN_NAMES # => ["Tom", "Alice", "Taro"]
```

```ruby
ADMIN_NAMES = ["Tom", "Alice"].freeze
ADMIN_NAMES << "Taro" # => RuntimeError: can't modify frozen Array
```

# Khi tạo 1 array,  dùng %w( )、%i( ) thay cho []

Trường hợp muốn tạo 1 array các chuỗi kí tự, dùng `%w( )` sẽ dễ viết và ngắn hơn. 

```ruby
actions = ['index', 'new', 'create']
```

```ruby
actions = %w(index new create) # => ['index', 'new', 'create']
```

Từ Ruby 2.0 trở đi, có cách viết `%i( )` để tạo symbol.

```ruby
actions = %i(index new create) # => [:index, :new, :create]
```

# Khi xử lý 1 array theo thứ tự,  dùng "&:method" thay cho "object.method"

```ruby
names = users.map{|user| user.name }
```

```ruby
names = users.map(&:name)
```

Không chỉ có `map` mà cả `each` ,`select` hay các block khác đều có thể dùng cách viết này. 

# Khi khai báo 1 số lớn, thêm "_" vào sẽ dễ đọc hơn

```ruby
ITEM_LIMIT = 1000000000
```

```ruby
ITEM_LIMIT = 1_000_000_000
```

# dùng attr_reader thay cho những method getter đơn giản

```ruby
class Person
  def initialize
    @name = "No name"
  end

  def name
    @name
  end
end
```

```ruby
class Person
  attr_reader :name

  def initialize
    @name = "No name"
  end

  # Không cần
  # def name
  #   @name
  # end
end
```


# Trong 1 array mà mỗi phần tử mang 1 ý nghĩa khác nhau, có thể lấy vào nhiều biến cùng lúc

```ruby
ans_array = 14.divmod(3)
puts "Thương #{ans_array[0]}"     # => Thương 4
puts "Số dư  #{ans_array[1]}"     # => Số dư  2
```

```ruby
quotient, remainder = 14.divmod(3)
puts "Thương #{quotient}"      # => Thương 4
puts "Số dư  #{remainder}"     # => Số dư  2
```

Tương tự với method `each` của 1 hash. 

```ruby
# key và value được lấy về dưới dạng array
{name: 'Tom', email: 'hoge@hoge.com'}.each do |key_and_value|
  puts "key: #{key_and_value[0]}"
  puts "value: #{key_and_value[1]}"
end
```

```ruby
# key và value được lưu vào 2 biến khác nhau
{name: 'Tom', email: 'hoge@hoge.com'}.each do |key, value|
  puts "key: #{key}"
  puts "value: #{value}"
end
```

# Khi nối nhiều array với nhau, không dùng + mà nên dùng *(splat)

```ruby
numbers = [1, 2, 3]
numbers_with_zero_and_100 = [0] + numbers + [100] # => [0, 1, 2, 3, 100]
```

```ruby
numbers = [1, 2, 3]
numbers_with_zero_and_100 = [0, *numbers, 100] # => [0, 1, 2, 3, 100]
```

Trong trường hợp bạn quên mất dấu `*` thì kết quả sẽ như sau :

```ruby
[0, numbers, 100] # => [0, [1, 2, 3], 100]
```

# Cách viết ||= khi cần "nếu nil thì init"

```ruby
def twitter_client
  @twitter_client = Twitter::REST::Client.new if @twitter_client.nil?
  @twitter_client
end
```

```ruby
def twitter_client
  @twitter_client ||= Twitter::REST::Client.new 
end
```

# Khi toàn bộ method là đối tượng của rescue thì lược bỏ begin/end

```ruby
def process_user(user)
  begin
    send_to_mail(user)
  rescue
    # xử lý 
  end
end
```

```ruby
def process_user(user)
  send_to_mail(user)
rescue
  # xử lý
end
```

# gọi rescue là StandardError thay vì Exception
Với những người đã từng học qua Java hay C# thì thường có thói quen sử dụng `Exception` để bắt lỗi ngoại lệ. 
Tuy nhiên, khi bắt `Exception` trong Ruby cũng đồng nghĩa với việc bắt luôn những lỗi cực kì nguy hiểm như `NoMemoryError`. 

`StandardError` là 1 subclass của `Exception` để bắt lỗi thực hiện của code. rescue mặc định là sẽ bắt `StandardError` và các subclass của nó nên có thể bỏ qua khi viết code. 

```ruby
def process_user(user)
  send_to_mail(user)
rescue Exception => ex
  # bắt cả những lỗi nguy hiểm như NoMemoryError,  ...
end
```

```ruby
def process_user(user)
  send_to_mail(user)
rescue => ex
  # bắt tất cả các lỗi thực hiện (StandardError và các subclass của nó)
end
```

# Khi thao tác với index, nêu dùng -1 thay vì size - 1
Code khi sử dụng size - 1
```ruby
numbers = [1, 2, 3, 4, 5]
name = 'Viet on Rails'

numbers[numbers.size - 1] # => 5
name[name.size - 1] # => 's'

numbers[1..numbers.size - 2] # => [2, 3, 4]
name[1..name.size - 2] # => "iet on Rail"
```

Code khi sử dụng -1
```ruby
numbers = [1, 2, 3, 4, 5]
name = 'Viet on Rails'

numbers[-1] # => 5
name[-1] # => 's'

numbers[1..-2] # => [2, 3, 4]
name[1..-2] # => "iet on Rail"
```

# find: trả lại yếu tố đầu tiên tìm thấy

```ruby
def find_admin(users)
  users.each do |user|
    return user if user.admin?
  end
  nil
end
```

```ruby
def find_admin(users)
  users.find(&:admin?)
end
```

Sử dụng `find_index` nếu muốn trả về index của yếu tố đầu tiên tìm thấy. 

# select: lấy tất cả những yếu tố thoả mãn điều kiện

```ruby
def find_admins(users)
  admins = []
  users.each do |user|
    admins << user if user.admin?
  end
  admins
end
```

```ruby
def find_admins(users)
  users.select(&:admin?)
end
```

Trái với `select` là `reject`, hàm này chỉ lấy những yếu tố `false`.

