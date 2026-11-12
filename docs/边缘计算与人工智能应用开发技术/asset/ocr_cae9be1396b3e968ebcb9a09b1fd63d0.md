```
std::vector<int> picked;
nms_sorted_bboxes(proposals, picked, nms_threshold);

int count = picked.size();

objects.clear();
objects.resize(count);
for (int i = 0; i < count; i++)
{
    objects[i] = proposals[picked[i]];

    //adjust offset to original unpadded
    float x0 = (objects[i].rect.x - (wpad / 2)) / scale;
    float y0 = (objects[i].rect.y - (hpad / 2)) / scale;
    float x1 = (objects[i].rect.x + objects[i].rect.width - (wpad / 2)) / scale;
    float y1 = (objects[i].rect.y + objects[i].rect.height - (hpad / 2)) / scale;
    /*
    float x0 = (objects[i].rect.x) / scale;
    float y0 = (objects[i].rect.y) / scale;
    float x1 = (objects[i].rect.x + objects[i].rect.width) / scale;
    float y1 = (objects[i].rect.y + objects[i].rect.height) / scale;
    */
    //clip
    x0 = std::max(std::min(x0, (float)(img_w - 1)), 0.f);
    y0 = std::max(std::min(y0, (float)(img_h - 1)), 0.f);
    x1 = std::max(std::min(x1, (float)(img_w - 1)), 0.f);
    y1 = std::max(std::min(y1, (float)(img_h - 1)), 0.f);

    objects[i].rect.x = x0;
    objects[i].rect.y = y0;
    objects[i].rect.width = x1 - x0;
    objects[i].rect.height = y1 - y0;
}

return 0;
}

static void draw_objects(const cv::Mat& bgr, const std::vector<Object>& objects)
{
    cv::Mat image = bgr.clone();
    for (size_t i = 0; i < objects.size(); i++)
    {
        const Object& obj = objects[i];
        fprintf(stderr, "%d = %5f at %2f.%2f 2f %2f x %2f\n", obj.label, obj.prob,
            obj.rect.x, obj.rect.y, obj.rect.width, obj.rect.height);

        cv::rectangle(image, obj.rect, cv::Scalar(0, 0, 255));

        char text[256];
```