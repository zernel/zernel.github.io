---
layout: post
title: "Uses rubyzip on rails 3"
category: Technology
tags: Rails
---

最近实现的zip下载功能：

### Model

    # app/models/downloader.rb
    require 'zip/zip'
    require 'zip/zipfilesystem'

    class Downloader
      attr_accessor :file_ids

      TEMP_PATH = "#{Rails.root.to_s}/public/temp_zips"

      def self.remove_temp(file_path)
        system "rm #{file_path}"
      end

      # ids should like this "1#2#3"
      def initialize(ids)
        ids ||= ''
        @file_ids = ids.split('#')
      end

      def add_file_id(file_id)
        return if @file_ids.include?(file_id)
        @file_ids << file_id
      end

      def delete_file_id(file_id)
        return unless @file_ids.include?(file_id)
        @file_ids.delete(file_id)
      end

      def files_list
        list = []
        @file_ids.each do |id|
          list << ProductDoc.find(id)
        end

        list
      end

      def compress
        file_path = "#{TEMP_PATH}/test-#{Time.now.strftime('%Y%m%d%I%L')}.zip"
        Zip::ZipFile.open(file_path, Zip::ZipFile::CREATE) do |zip|
          files_list.each do |file|
            path = file.document.path
            zip.add(File.basename(path), path)
          end
        end
        return file_path
      end
    end

### Controller

    def download_zip
      zipfile = downloader.compress
      if File.exist?(zipfile)
        send_file zipfile, :type => 'application/zip', :filename => "Test-#{Time.now.strftime('%Y%m%d%I')}.zip", :disposition => 'attachment'
        Downloader.delay(run_at: 30.minutes.from_now).remove_temp(zipfile)
        cookies[:download_list] = nil
      end
    end

    # app/controllers/application_controller.rb
    helper_method :downloader
    def downloader
      return @downloader if @downloader
      @downloader = Downloader.new(cookies[:download_list])
    end 

参考：http://hi.baidu.com/encore_shao/item/4509551743db8758f0090e52
