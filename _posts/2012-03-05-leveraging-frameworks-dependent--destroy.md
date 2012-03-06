---
layout: post
title: "Leveraging Frameworks: dependent => :destroy"
category:
tags: [Code, Rails]
---
{% include JB/setup %}

Maybe you have some code like

    model Foo < ActiveRecord::Base
        has_many :bars
        before_destroy :clean_up_bars

        def clean_up_bars
           self.bars.destroy_all
        end
    end

My main gripe with this, is that Rails provides an awesome way to
clean up any thing that doesn't make sense without a parent.
Fortunately it is even super simple.


    model Foo < ActiveRecord::Base
        has_many :bars
        before_destroy :clean_up_bars, :dependent => :destroy
    end

This will make it so that when your Foo instance is destroyed, it will
also destroy each of the bars that belong to that Foo instance.
