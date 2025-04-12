# NUS Technology

# Rails Best Practices

### Table of Contents

- [1. Model](#1)
- [2. Controller](#2)
- [3. View](#3)
- [4. Unit test](#4)
- [5. Common](#5)

### <span id="1"></span>1. Model

- Use named scope instead of using where everywhere
- Always use lambda for named scope

  **Example:**

  ```ruby
  scope :today, → { where(published_date: Date.today) }
  instead of:
  scope :today, where(published_date: Date.today))
  ```

- Always create validations if any. Don't rely on client-side validations
- Always reject SQL injection

  **Example:**

  ```ruby
  User.where("name = ?", params[:name])
  instead of:
  User.where("name = '#{params[:name]}'")
  ```

- Always create needed indexes to improve performance
- Use seeds.rb instead of migration to create seed data
- Use “includes” to prevent N+1 queries
- Use “size” instead of “count”
- Use “.find_each” instead of “.all.each”. “.find_each” split pulling data query to multiple times, 1000 (default) records each time. This is called batch finder
- Only select needed fields instead of select all (SELECT \* FROM...)
- Avoid using of raw SQL inside Rails
- Always add default value for Boolean attributes

### <span id="2"></span>2. Controller

- Controller should be lightweight, complex logic should be moved to service layer
- Should follow RESTful as most as possible
- Create separated controllers with namespace (e.g: api/v1) for API
- Use before_filter to do a repeat task

  **Example:**

  ```ruby
  class PostsController < ApplicationController
    before_filter :get_post, :only => [:edit, :update, :destroy]
    def edit
      ...
    end
    def update
      ...
    end
    def destroy
      ...
    end
    private
    def get_post
      @post = Post.find(params[:id])
    end
  end
  instead of:
  class PostsController < ApplicationController
    def edit
      @post = Post.find(params[:id])
      ...
    end
    def update
      @post = Post.find(params[:id])
      ...
    end
    def destroy
      @post = Post.find(params[:id])
      ...
    end
  end
  ```

- Don't modify the params hash. The params hash contains all the data that was submitted from a request. If you modify it, later code won't have access to it. Instead, copy the params hash and modify the copy.

### <span id="3"></span>3. View

- Use partials instead of a fat view
- Data that a partial needs should be sent to it as parameters instead of using instance variable (@user)
- Don't use queries in views
- Limit of using inline js, css
- Use js-routes gem to create path instead of using hardcoded path
- Use i18n-js gem to do translation in js
- Always use I18n instead of hardcoded texts
- When using theme, use 2-3 css files instead of only application.css to prevent IE class count limit in a css file
- Any time a form is shown on a page, the first or "most important" field should automatically have the focus

### <span id="4"></span>4. Unit test

- Be clear about what method you are describing
  For instance, use the Ruby documentation convention of . (or ::) when referring to a class method's name and # when referring to an instance method's name.

  ```
  #BAD
  describe 'the authenticate method for User' do
  describe 'if the user is an admin' do

  #GOOD
  describe '.authenticate' do
  describe '#admin?' do
  ```

- Use contexts
  Contexts are a powerful method to make your tests clear and well organized. In the long term this practice will keep tests easy to read.

  ```
  #BAD
  it 'has 200 status code if logged in' do
    expect(response).to respond_with 200
  end
  it 'has 401 status code if not logged in' do
    expect(response).to respond_with 401
  end

  #GOOD
  context 'when logged in' do
    it { is_expected.to respond_with 200 }
  end
  context 'when logged out' do
    it { is_expected.to respond_with 401 }
  end
  ```

- Keep your description short
  A spec description should never be longer than 40 characters. If this happens you should split it using a context.

  ```
  #BAD
  it 'has 422 status code if an unexpected params will be added' do

  #GOOD
  context 'when not valid' do
    it { is_expected.to respond_with 422 }
  end
  ```

- Single expectation test
  The 'one expectation' tip is more broadly expressed as 'each test should make only one assertion'. This helps you on finding possible errors, going directly to the failing test, and to make your code readable.

In isolated unit specs, you want each example to specify one (and only one) behavior. Multiple expectations in the same example are a signal that you may be specifying multiple behaviors.

Anyway, in tests that are not isolated (e.g. ones that integrate with a DB, an external webservice, or end-to-end-tests), you take a massive performance hit to do the same setup over and over again, just to set a different expectation in each test. In these sorts of slower tests, I think it's fine to specify more than one isolated behavior.

```
# GOOD (ISOLATED)
it { is_expected.to respond_with_content_type(:json) }
it { is_expected.to assign_to(:resource) }

#GOOD (NOT ISOLATED)
it 'creates a resource' do
  expect(response).to respond_with_content_type(:json)
  expect(response).to assign_to(:resource)
end
```

- Test all possible cases
- Expect vs Should syntax
  On new projects always use the expect syntax.
- Use subject
  If you have several tests related to the same subject use subject{} to DRY them up.

  ```
  #BAD
  it { expect(assigns('message')).to match /it was born in Belville/ }

  #GOOD
  subject { assigns('message') }
  it { is_expected.to match /it was born in Billville/ }
  ```

- Use let and let!
  When you have to assign a variable instead of using a before block to create an instance variable, use let. Using let the variable lazy loads only when it is used the first time in the test and get cached until that specific test is finished. A really good and deep description of what let does can be found in this stackoverflow answer.

  ```
  #BAD
  describe '#type_id' do
    before { @resource = FactoryGirl.create :device }
    before { @type     = Type.find @resource.type_id }

    it 'sets the type_id field' do
      expect(@resource.type_id).to equal(@type.id)
    end
  end

  #GOOD
  describe '#type_id' do
    let(:resource) { FactoryGirl.create :device }
    let(:type)     { Type.find resource.type_id }

    it 'sets the type_id field' do
      expect(resource.type_id).to equal(type.id)
    end
  end

  ```

Use let to initialize actions that are lazy loaded to test your specs.

```
context 'when updates a not existing property value' do
  let(:properties) { { id: Settings.resource_id, value: 'on'} }

  def update
    resource.properties = properties
  end

  it 'raises a not found error' do
    expect { update }.to raise_error Mongoid::Errors::DocumentNotFound
  end
end
```

- Create only the data you need
- Use factories and not fixtures
- Test what you see
  Deeply test your models and your application behaviour (integration tests). Do not add useless complexity testing controllers.

When I first started testing my apps I was testing controllers, now I don't. Now I only create integration tests using RSpec and Capybara. Why? Because I truly believe that you should test what you see and because testing controllers is an extra step you don't need. You'll find out that most of your tests go into the models and that integration tests can be easily grouped into shared examples, building a clear and readable test suite.

This is an open debate in the Ruby community and both sides have good arguments supporting their idea. People supporting the need of testing controllers will tell you that your integration tests don't cover all use cases and that they are slow.

Both are wrong. You can easily cover all use cases (why shouldn't you?) and you can run single file specs using automated tools like Guard. In this way you will run only the specs you need to test blazing fast without stopping your flow.

- Automatic tests with guard
- Stubbing HTTP requests
  Sometimes you need to access external services. In these cases you can't rely on the real service but you should stub it with solutions like webmock.

```
context "with unauthorized access" do
  let(:uri) { 'http://api.lelylan.com/types' }
  before    { stub_request(:get, uri).to_return(status: 401, body: fixture('401.json')) }
  it "gets a not authorized notification" do
    page.driver.get uri
    expect(page).to have_content 'Access denied'
  end
end
```

- Useful formatter
  Use a formatter that can give you useful information about the test suite. I personally find fuubar really nice. To make it work add the gem and set fuubar as default formatter in your Guardfile.

### <span id="5"></span>5. Common

- Never use magic numbers. Use constants, configs instead
- Use I18n instead of hardcode texts
- Don't use “rescue Exception => e”, use “rescue StandardError => e” or “rescue => e” instead
- Remove unused code instead of making it to be a comment
- Prevent duplicated code: use functions instead of copying and pasting code
- Use meaningful names for variables, functions instead of a, b...
- Use Time.zone.now instead of Time.now. The ActiveSupport method Time.zone.now should be used in place of the Ruby method Time.now to pickup the local time zone.
- Several blocks of very similar code should be combined into common functions.
- Use another has_many associations to load only proper fields for Object which have alot of fields, so we can use include easier:

```
  class Location < ActiveRecord::Base
    has_many :listings, :dependent => :destroy
    has_many :listing_ids_only, class_name: "Listing", select: "listings.id, listings.location_id", validate: false, readonly: true
    has_many :active_listings, class_name: "Listing", conditions: {active: true}
    has_many :listings_proper, class_name: "Listing", select: "listings.id, listings.location_id, listings.name, listings.address"
  end

  ### Use Location.includes(:listings_proper).all instead of Location.includes(:listings).all

```

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
