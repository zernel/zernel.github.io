---
layout: post
title: "Rails3中导出CSV文件"
category: Technology
tags: Rails
---

因为项目需要，今天看了一下关于导出CSV文件的做法，从 Ruby 1.9.2 开始，`fastercsv`作为标准库整合到 Ruby 之中，我们可以直接通过直接 require 使用。下面例子是要把项目中的`auctions`数据导出来，具体做法参考如下：

### View

    <%= link_to 'Export to CSV', auctions_path(format: 'csv') %>

### Controller

    require 'csv'

    class AuctionsController < ApplicationController
      def index
        ...
        @auctions = ...

        respond_to do |format|
          format.csv {
            send_data(csv_content_for(@auctions),
                      type: "text/csv;charset=utf-8; header=present",
                      filename: "Report_auctions_#{Time.now.strftime("%Y%m%d")}.csv")
          }
          format.html
        end
      end

      ...

      private
      def csv_content_for(auctions)
        CSV.generate do |csv|
          csv << ["VIN", "YEAR", "MAKE", "MODEL", "NAME", "STATUS"]

          auctions.each do |auction|
            vehicle = auction.vehicle
            csv << [vehicle.vin, vehicle.year, vehicle.make, vehicle.model, vehicle.user.name, auction.auction_status.name]
          end
        end
      end
    end

在generate CSV的时候每插入一个数组都会自动转换成以逗号分隔的字符串并在后面自动加上换行符，如下：

      ➜  ~  irb
      1.9.3-p0 :001 > require 'csv'
       => true 
      1.9.3-p0 :002 > ['a', 'b', 'c'].to_csv
       => "a,b,c\n" 

因而每个数组为一行，到时在Excel只要导入的时候以逗号作为分隔符就可以排出自己想要的效果了：）
